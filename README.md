#!/bin/bash
# SISTEMA DANSSH BUNNY PRO - VERSÃO FINAL
# NOME PERSONALIZADO | CORES | HTTP INJEÇÃO | SEGURANÇA TOTAL
# USUÁRIO: Dandandan0307 | REPOSITÓRIO: Dansshnetxp

# ---------------- CONFIGURAÇÕES DEFINITIVAS ----------------
NOME_SISTEMA="🐇 𝐃𝐀𝐍𝐒𝐒𝐇 𝐁𝐔𝐍𝐍𝐘 𝐏𝐑𝐎 🐇"
NOME_CURTO="DANSSH-BUNNY"
COR_PRINCIPAL='\033[1;35m'   # ROSA BONITO
COR_DESTAQUE='\033[1;96m'   # CIANO
COR_SUCESSO='\033[1;92m'    # VERDE
COR_ERRO='\033[1;91m'       # VERMELHO
RESET='\033[0m'

# ---------------- LOGO E VISUAL ----------------
logo() {
clear
echo -e "${COR_PRINCIPAL}"
echo " ██████╗  █████╗ ███╗   ██╗███████╗██╗  ██╗"
echo "██╔════╝ ██╔══██╗████╗  ██║██╔════╝██║  ██║"
echo "██║  ███╗███████║██╔██╗ ██║███████╗███████║"
echo "██║   ██║██╔══██║██║╚██╗██║╚════██║██╔══██║"
echo "╚██████╔╝██║  ██║██║ ╚████║███████║██║  ██║"
echo " ╚═════╝ ╚═╝  ╚═╝╚═╝  ╚═══╝╚══════╝╚═╝  ╚═╝"
echo -e "${COR_DESTAQUE}✨ $NOME_SISTEMA ✨${RESET}"
echo -e "${COR_SUCESSO}💡 SEMPRE QUE QUISER ENTRAR → DIGITE: danssh${RESET}"
echo -e "${COR_PRINCIPAL}=============================================${RESET}"
}

# VERIFICA ROOT
if [ "$(id -u)" -ne 0 ]; then
    echo -e "${COR_ERRO}❌ ERRO: Execute como ROOT!${RESET}"
    exit 1
fi

# MENU PRINCIPAL
menu_principal() {
logo
echo -e "${COR_DESTAQUE}📋 MENU PRINCIPAL - TODAS FUNÇÕES${RESET}"
echo -e "${COR_PRINCIPAL}1 → INSTALAR SISTEMA COMPLETO${RESET}"
echo -e "${COR_PRINCIPAL}2 → GERENCIAR USUÁRIOS SSH${RESET}"
echo -e "${COR_PRINCIPAL}3 → GERENCIAR PROXY / HTTP INJEÇÃO${RESET}"
echo -e "${COR_PRINCIPAL}4 → GERENCIAR PORTAS${RESET}"
echo -e "${COR_PRINCIPAL}5 → CONFIGURAÇÕES SSH${RESET}"
echo -e "${COR_PRINCIPAL}6 → 🚀 XRAY / V2RAY / VLESS / VMESS${RESET}"
echo -e "${COR_PRINCIPAL}7 → BLOQUEAR / LIBERAR PAÍSES${RESET}"
echo -e "${COR_PRINCIPAL}8 → FIREWALL COMPLETO${RESET}"
echo -e "${COR_PRINCIPAL}9 → TÚNEIS E REDIRECIONAMENTOS${RESET}"
echo -e "${COR_PRINCIPAL}10 → VER TODAS CONFIGURAÇÕES${RESET}"
echo -e "${COR_PRINCIPAL}11 → REINICIAR TODOS SERVIÇOS${RESET}"
echo -e "${COR_PRINCIPAL}12 → SAIR DO PAINEL${RESET}"
echo -e "${COR_PRINCIPAL}=============================================${RESET}"
read -p "Escolha uma opção: " opcao

case $opcao in
    1) instalar_sistema ;;
    2) menu_usuarios ;;
    3) menu_proxy ;;
    4) menu_portas ;;
    5) menu_ssh ;;
    6) menu_xray_v2ray ;;
    7) menu_paises ;;
    8) menu_firewall ;;
    9) menu_tuneis ;;
    10) ver_configuracoes ;;
    11) reiniciar_servicos ;;
    12) exit 0 ;;
    *) echo -e "${COR_ERRO}❌ Opção inválida!${RESET}"; sleep 2; menu_principal ;;
esac
}

# INSTALAR TUDO
instalar_sistema() {
logo
echo -e "${COR_DESTAQUE}🔄 Instalando $NOME_SISTEMA...${RESET}"

apt update -y && apt upgrade -y
apt install -y zip unzip curl wget git ufw mysql-server php8.4-fpm nginx squid openssh-server iptables net-tools geoip-bin uuid-runtime

systemctl enable --now php8.4-fpm nginx ssh squid

# INSTALAR XRAY
bash -c "$(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh)" @ install

# CONFIG XRAY
UUID=$(uuidgen)
mkdir -p /usr/local/etc/xray
cat > /usr/local/etc/xray/config.json << EOF
{"log":{"loglevel":"warning"},"inbounds":[{"port":8880,"protocol":"vmess","settings":{"clients":[{"id":"$UUID","alterId":0}]}},{"port":8881,"protocol":"vless","settings":{"clients":[{"id":"$UUID"}],"decryption":"none"}},{"port":8882,"protocol":"trojan","settings":{"clients":[{"password":"$UUID"}]}}],"outbounds":[{"protocol":"freedom"}]}
EOF

systemctl enable --now xray

# PORTAS LIBERADAS
ufw allow 22,80,443,3128,8080,8880,8881,8882/tcp
ufw --force enable

# COMANDO DANSSH
cp "$0" /usr/local/bin/danssh
chmod +x /usr/local/bin/danssh
cp "$0" /bin/danssh
chmod +x /bin/danssh

IP_SERVIDOR=$(hostname -I | awk '{print $1}')

echo -e "${COR_SUCESSO}✅ $NOME_SISTEMA INSTALADO COM SUCESSO!${RESET}"
echo -e "${COR_DESTAQUE}🔹 IP: $IP_SERVIDOR${RESET}"
echo -e "${COR_DESTAQUE}🔹 UUID: $UUID${RESET}"
echo -e "${COR_DESTAQUE}🔗 VMESS: $(echo -n '{"add":"'$IP_SERVIDOR'","aid":"0","id":"'$UUID'","net":"tcp","path":"","port":"8880","ps":"'$NOME_SISTEMA'","scy":"auto","tls":"","type":"none","v":"2"}' | base64 -w 0 | sed 's/^/vmess:\/\//')${RESET}"
echo -e "${COR_DESTAQUE}🔗 VLESS: vless://$UUID@$IP_SERVIDOR:8881?encryption=none&security=none&type=tcp#'$NOME_SISTEMA'${RESET}"
echo -e "${COR_DESTAQUE}🔗 TROJAN: trojan://$UUID@$IP_SERVIDOR:8882?security=none&type=tcp#'$NOME_SISTEMA'${RESET}"
echo -e "${COR_DESTAQUE}🌐 HTTP INJEÇÃO: $IP_SERVIDOR:3128#$NOME_CURTO-BUNNY${RESET}"
read -p "Pressione ENTER..."
menu_principal
}

# USUÁRIOS SSH
menu_usuarios() {
logo
echo -e "${COR_DESTAQUE}👤 GERENCIAR USUÁRIOS SSH${RESET}"
echo -e "${COR_PRINCIPAL}1 → CRIAR USUÁRIO${RESET}"
echo -e "${COR_PRINCIPAL}2 → ALTERAR SENHA${RESET}"
echo -e "${COR_PRINCIPAL}3 → EXCLUIR USUÁRIO${RESET}"
echo -e "${COR_PRINCIPAL}4 → LISTAR TODOS${RESET}"
echo -e "${COR_PRINCIPAL}5 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) 
        read -p "Nome: " u; read -p "Senha: " p; 
        useradd -m -s /bin/bash $u; echo "$u:$p"|chpasswd; 
        echo -e "${COR_SUCESSO}✅ USUÁRIO $NOME_SISTEMA CRIADO: $u${RESET}"
        echo -e "${COR_DESTAQUE}🔹 CONEXÃO SSH: $u:$p@$(hostname -I | awk '{print $1}'):22#$NOME_SISTEMA${RESET}"
    ;;
    2) read -p "Usuário: " u; read -p "Nova senha: " p; echo "$u:$p"|chpasswd;;
    3) read -p "Usuário: " u; userdel -r $u; echo -e "${COR_SUCESSO}✅ Excluído!${RESET}";;
    4) cat /etc/passwd|grep -E 'bash|sh'|cut -d: -f1;;
esac
read -p "ENTER..."
menu_usuarios
}

# PROXY / HTTP INJEÇÃO
menu_proxy() {
logo
echo -e "${COR_DESTAQUE}🌐 PROXY / HTTP INJEÇÃO - $NOME_SISTEMA${RESET}"
echo -e "${COR_PRINCIPAL}1 → ATIVAR${RESET}"
echo -e "${COR_PRINCIPAL}2 → DESATIVAR${RESET}"
echo -e "${COR_PRINCIPAL}3 → CRIAR USUÁRIO PROXY${RESET}"
echo -e "${COR_PRINCIPAL}4 → VER DADOS DE INJEÇÃO${RESET}"
echo -e "${COR_PRINCIPAL}5 → VOLTAR${RESET}"
read -p "Escolha: " op
IP_ATUAL=$(hostname -I | awk '{print $1}')
case $op in
    1) systemctl start squid; ufw allow 3128,8080/tcp; echo -e "${COR_SUCESSO}✅ PROXY $NOME_SISTEMA ATIVO!${RESET}";;
    2) systemctl stop squid; echo -e "${COR_SUCESSO}✅ Desativado!${RESET}";;
    3) read -p "Usuário: " u; read -p "Senha: " p; echo "$u:$p" >> /etc/squid/passwd; systemctl restart squid;;
    4)
        echo -e "${COR_DESTAQUE}📋 DADOS PARA HTTP INJEÇÃO:${RESET}"
        echo -e "${COR_SUCESSO}🔹 IP: $IP_ATUAL${RESET}"
        echo -e "${COR_SUCESSO}🔹 PORTA: 3128 | 8080${RESET}"
        echo -e "${COR_SUCESSO}🔹 NOME EXIBIDO: $NOME_SISTEMA${RESET}"
        echo -e "${COR_SUCESSO}🔹 LINHA PRONTA: $IP_ATUAL:3128#$NOME_CURTO-BUNNY${RESET}"
    ;;
esac
read -p "ENTER..."
menu_proxy
}

# PORTAS
menu_portas() {
logo
echo -e "${COR_DESTAQUE}🔌 GERENCIAR PORTAS${RESET}"
echo -e "${COR_PRINCIPAL}1 → ABRIR PORTA${RESET}"
echo -e "${COR_PRINCIPAL}2 → FECHAR PORTA${RESET}"
echo -e "${COR_PRINCIPAL}3 → VER PORTAS ABERTAS${RESET}"
echo -e "${COR_PRINCIPAL}4 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) read -p "Porta: " p; ufw allow $p/tcp;;
    2) read -p "Porta: " p; ufw delete allow $p/tcp;;
    3) ufw status numbered;;
esac
read -p "ENTER..."
menu_portas
}

# SSH
menu_ssh() {
logo
echo -e "${COR_DESTAQUE}⚙️ CONFIGURAÇÕES SSH${RESET}"
echo -e "${COR_PRINCIPAL}1 → ALTERAR PORTA${RESET}"
echo -e "${COR_PRINCIPAL}2 → VER PORTA ATUAL${RESET}"
echo -e "${COR_PRINCIPAL}3 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) read -p "Nova porta: " p; sed -i "s/^Port .*/Port $p/" /etc/ssh/sshd_config; ufw allow $p/tcp; systemctl restart ssh;;
    2) grep "^Port " /etc/ssh/sshd_config|awk '{print $2}';;
esac
read -p "ENTER..."
menu_ssh
}

# XRAY / V2RAY
menu_xray_v2ray() {
logo
echo -e "${COR_DESTAQUE}🚀 XRAY / V2RAY - $NOME_SISTEMA${RESET}"
echo -e "${COR_PRINCIPAL}1 → STATUS${RESET}"
echo -e "${COR_PRINCIPAL}2 → CRIAR NOVO USUÁRIO (LINKS PRONTOS)${RESET}"
echo -e "${COR_PRINCIPAL}3 → VER DADOS E LINHAS${RESET}"
echo -e "${COR_PRINCIPAL}4 → REINICIAR${RESET}"
echo -e "${COR_PRINCIPAL}5 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) echo -e "XRAY: $(systemctl is-active xray)";;
    2)
        NOVO_UUID=$(uuidgen)
        IP_ATUAL=$(hostname -I | awk '{print $1}')
        sed -i "s/\"id\": \"[^\"]*\"/\"id\": \"$NOVO_UUID\"/g" /usr/local/etc/xray/config.json
        sed -i "s/\"password\": \"[^\"]*\"/\"password\": \"$NOVO_UUID\"/g" /usr/local/etc/xray/config.json
        systemctl restart xray
        echo -e "${COR_SUCESSO}✅ NOVO USUÁRIO $NOME_SISTEMA CRIADO!${RESET}"
        echo -e "${COR_DESTAQUE}🔹 IP: $IP_ATUAL${RESET}"
        echo -e "${COR_DESTAQUE}🔹 UUID: $NOVO_UUID${RESET}"
        echo -e "${COR_DESTAQUE}🔗 VMESS → $(echo -n '{"add":"'$IP_ATUAL'","aid":"0","id":"'$NOVO_UUID'","net":"tcp","path":"","port":"8880","ps":"'$NOME_SISTEMA'","scy":"auto","tls":"","type":"none","v":"2"}' | base64 -w 0 | sed 's/^/vmess:\/\//')${RESET}"
        echo -e "${COR_DESTAQUE}🔗 VLESS → vless://$NOVO_UUID@$IP_ATUAL:8881?encryption=none&security=none&type=tcp#'$NOME_SISTEMA'${RESET}"
        echo -e "${COR_DESTAQUE}🔗 TROJAN → trojan://$NOVO_UUID@$IP_ATUAL:8882?security=none&type=tcp#'$NOME_SISTEMA'${RESET}"
        echo -e "${COR_DESTAQUE}🌐 HTTP INJEÇÃO: $IP_ATUAL:3128#$NOME_CURTO-BUNNY${RESET}"
        ;;
    3)
        IP_ATUAL=$(hostname -I | awk '{print $1}')
        UUID_ATUAL=$(grep -o '"id": "[^"]*"' /usr/local/etc/xray/config.json | head -n1 | cut -d'"' -f4)
        echo -e "${COR_DESTAQUE}🔹 IP: $IP_ATUAL${RESET}"
        echo -e "${COR_DESTAQUE}🔹 UUID: $UUID_ATUAL${RESET}"
        echo -e "${COR_DESTAQUE}🔗 VMESS → $(echo -n '{"add":"'$IP_ATUAL'","aid":"0","id":"'$UUID_ATUAL'","net":"tcp","path":"","port":"8880","ps":"'$NOME_SISTEMA'","scy":"auto","tls":"","type":"none","v":"2"}' | base64 -w 0 | sed 's/^/vmess:\/\//')${RESET}"
        echo -e "${COR_DESTAQUE}🔗 VLESS → vless://$UUID_ATUAL@$IP_ATUAL:8881?encryption=none&security=none&type=tcp#'$NOME_SISTEMA'${RESET}"
        echo -e "${COR_DESTAQUE}🔗 TROJAN → trojan://$UUID_ATUAL@$IP_ATUAL:8882?security=none&type=tcp#'$NOME_SISTEMA'${RESET}"
        echo -e "${COR_DESTAQUE}🌐 HTTP INJEÇÃO: $IP_ATUAL:3128#$NOME_CURTO-BUNNY${RESET}"
        ;;
    4) systemctl restart xray;;
esac
read -p "ENTER..."
menu_xray_v2ray
}

# PAÍSES
menu_paises() {
logo
echo -e "${COR_DESTAQUE}🌍 BLOQUEAR / LIBERAR PAÍSES${RESET}"
echo -e "${COR_PRINCIPAL}1 → BLOQUEAR PAÍS${RESET}"
echo -e "${COR_PRINCIPAL}2 → LIBERAR PAÍS${RESET}"
echo -e "${COR_PRINCIPAL}3 → SÓ BRASIL LIVRE${RESET}"
echo -e "${COR_PRINCIPAL}4 → LIMPAR TUDO${RESET}"
echo -e "${COR_PRINCIPAL}5 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) read -p "Código ex: BR, US: " c; iptables -A INPUT -m geoip --src-cc $c -j DROP;;
    2) read -p "Código: " c; iptables -D INPUT -m geoip --src-cc $c -j DROP;;
    3) iptables -F; iptables -A INPUT -m geoip ! --src-cc BR -j DROP;;
    4) iptables -F;;
esac
read -p "ENTER..."
menu_paises
}

# FIREWALL
menu_firewall() {
logo
echo -e "${COR_DESTAQUE}🔥 FIREWALL - $NOME_SISTEMA${RESET}"
echo -e "${COR_PRINCIPAL}1 → MODO SEGURO${RESET}"
echo -e "${COR_PRINCIPAL}2 → LIBERAR TUDO${RESET}"
echo -e "${COR_PRINCIPAL}3 → BLOQUEAR IP${RESET}"
echo -e "${COR_PRINCIPAL}4 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) ufw reset; ufw default deny incoming; ufw allow 22,80,443,3128,8080,8880,8881,8882/tcp; ufw --force enable;;
    2) ufw reset; ufw default allow all; ufw --force enable;;
    3) read -p "IP: " ip; ufw deny from $ip;;
esac
read -p "ENTER..."
menu_firewall
}

# TÚNEIS
menu_tuneis() {
logo
echo -e "${COR_DESTAQUE}🔁 TÚNEIS E REDIRECIONAMENTOS${RESET}"
echo -e "${COR_PRINCIPAL}1 → REDIRECIONAR PORTA${RESET}"
echo -e "${COR_PRINCIPAL}2 → VOLTAR${RESET}"
read -p "Escolha: " op
case $op in
    1) read -p "Entrada: " p1; read -p "Saída: " p2; iptables -t nat -A PREROUTING -p tcp --dport $p1 -j REDIRECT --to-port $p2;;
esac
read -p "ENTER..."
menu_tuneis
}

# CONFIGS
ver_configuracoes() {
logo
echo -e "${COR_DESTAQUE}📋 CONFIGURAÇÕES - $NOME_SISTEMA${RESET}"
echo -e "IP: $(hostname -I|awk '{print $1}')"
echo -e "SSH Porta: $(grep ^Port /etc/ssh/sshd_config|awk '{print $2}')"
echo -e "Nome Exibido: $NOME_SISTEMA"
read -p "ENTER..."
menu_principal
}

# REINICIAR
reiniciar_servicos() {
logo
echo -e "${COR_DESTAQUE}🔄 Reiniciando $NOME_SISTEMA...${RESET}"
systemctl restart ssh nginx squid xray ufw
echo -e "${COR_SUCESSO}✅ Pronto!${RESET}"
read -p "ENTER..."
menu_principal
}

# INICIAR
menu_principal
