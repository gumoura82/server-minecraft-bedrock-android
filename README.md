# SERVIDOR DEDICADO MINECRAFT BEDROCK NO ANDROID

### Guia Completo (Local + Online)

Este tutorial ensina como transformar um smartphone Android potente em um **Servidor Dedicado oficial do Minecraft Bedrock**, rodando via Termux + Ubuntu + Box64. O método foi testado com **horas de uptime estável**, executando a versão **1.21.124** do Bedrock Server. No caso usei o celular ASUS ROG Phone 5s, mas pode ser qualquer outro celular que tenha uma potência boa.



## 📋 Índice

1. [Servidor Local (LAN)](#1-servidor-local-lan)
2. [Servidor Online (Internet)](#2-servidor-online-internet)
3. [Configuração do Servidor](#3-configuração-do-servidor)
4. [Manutenção e Backups](#4-manutenção-e-backups)
5. [Monitoramento e Logs](#5-monitoramento-e-logs)
6. [Otimização ROG Phone 5s](#6-otimização-rog-phone-5s)
7. [Troubleshooting](#7-troubleshooting)
8. [FAQ](#8-faq)
9. [Resumo](#9-resumo)



## 1. SERVIDOR LOCAL (LAN)

**Até o final desta seção, o servidor já estará funcionando localmente.**  
Nesta parte ele só será acessível para jogadores na mesma rede Wi-Fi/LAN.

Depois disso, na seção Servidor Online, você aprenderá a liberar acesso via internet usando Playit.

### 1.1 Objetivo

Rodar o servidor oficial da Mojang (x86_64) em um celular ARM64 sem perda de desempenho usando:

- Termux
- Ubuntu via proot-distro
- Box64

### 1.2 Requisitos

**Hardware recomendado:**

- Snapdragon 800+
- 6 GB RAM (mínimo)
- 8–12 GB RAM (ideal)
- Adaptador USB-C → Ethernet (para máxima estabilidade)

**Software:**

- Termux (F-Droid recomendado)
- proot-distro
- Ubuntu
- Box64

### 1.3 Instalando Ubuntu no Termux

```bash
pkg update && pkg upgrade
pkg install proot-distro
proot-distro install ubuntu
proot-distro login ubuntu
```

### 1.4 Instalando o Box64

```bash
apt update && apt install wget gpg -y
wget https://ryanfortner.github.io/box64-debs/box64.list -O /etc/apt/sources.list.d/box64.list
wget -qO- https://ryanfortner.github.io/box64-debs/KEY.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/box64.gpg
apt update && apt install box64 -y
```

### 1.5 Instalando o Servidor Bedrock (Método Manual)

#### A) Baixar pelo navegador (Chrome)

Acesse: [https://www.minecraft.net/pt-br/download/server/bedrock](https://www.minecraft.net/pt-br/download/server/bedrock)  
Baixe a versão **Ubuntu (Linux)**.

#### B) Instalar no Termux (Ubuntu)

```bash
mkdir servidor_estavel
cd servidor_estavel
cp /sdcard/Download/bedrock-server-*.zip server.zip
unzip -o server.zip
chmod +x bedrock_server
```

### 1.6 Script Anti-Crash (Essencial)

Crie o arquivo de inicialização com o comando abaixo:

```bash
cat <<EOF > iniciar.sh
#!/bin/bash
while true
do
    echo "---------------------------------------"
    echo " 🛡️ INICIANDO MODO ESTÁVEL (ROG SERVER)..."
    echo " (CTRL + C para parar)"
    echo "---------------------------------------"
    export BOX64_DYNAREC_BIGBLOCK=0
    export BOX64_DYNAREC_STRONGMEM=1
    LD_LIBRARY_PATH=. box64 ./bedrock_server
    echo "---------------------------------------"
    echo " ⚠️ CRASH DETECTADO – REINICIANDO EM 5s..."
    echo "---------------------------------------"
    sleep 5
done
EOF
chmod +x iniciar.sh
```

### 1.7 Iniciando o Servidor Local

```bash
./iniciar.sh
```

Agora o servidor já funciona **somente para jogadores da mesma rede local** (LAN/Wi-Fi).  
Se quiser jogar online, continue para a próxima parte.



## 2. SERVIDOR ONLINE (INTERNET)

### Usando Playit.gg (sem abrir portas do roteador)

Aqui você vai:

1. Criar uma conta Playit
2. Registrar o agente rodando no celular
3. Vincular o agente à conta
4. Criar um túnel Minecraft Bedrock
5. Receber um IP público para seus amigos

### 2.1 ⚠️ IMPORTANTE – Duas Sessões do Termux Simultâneas

O servidor Minecraft e o Playit devem rodar juntos. Por isso, você precisa abrir duas sessões:

- **Sessão 1 → Servidor** (roda o `./iniciar.sh`)
- **Sessão 2 → Playit** (roda o agente do Playit)

#### Como abrir duas sessões no Termux

1. Com o Termux aberto, **arraste o dedo da esquerda para a direita** → Isso abre o **Menu Lateral**
2. Toque em **New Session** ou no símbolo **＋**
3. Agora você tem:
   - Sessão 0 (Servidor)
   - Sessão 1 (Playit)
4. Alternar entre elas usando esse mesmo menu

**Isso é obrigatório** — se um dos dois parar, o servidor online cai.

### 2.2 Instalando o agente do Playit (Sessão 2)

```bash
wget https://github.com/playit-cloud/playit-agent/releases/latest/download/playit-linux-aarch64
chmod +x playit-linux-aarch64
./playit-linux-aarch64
```

Ele mostrará:

```
To link this agent visit:
https://playit.gg/claim/AGENT-ID-AQUI
```

### 2.3 Criando conta e vinculando o celular

1. Acesse o link mostrado no terminal
2. Crie sua conta ou faça login
3. O site vai registrar automaticamente **seu celular como um Agente Playit**
4. O terminal exibirá "Linked Successfully"

### 2.4 Criando o túnel Minecraft Bedrock

No site do Playit:

1. Vá em **Tunnels**
2. Clique em **New Tunnel**
3. Tipo: **Minecraft Bedrock (UDP)**
4. Porta local: **19132**
5. Selecione o agente do seu celular
6. Salve

Ele gerará um IP como:

```
12345.playit.gg:19132
```

### 2.5 Rodando tudo junto

#### Sessão 1 → Servidor

```bash
./iniciar.sh
```

#### Sessão 2 → Playit

```bash
./playit-linux-aarch64
```

✅ Pronto — servidor online, estável e com IP público.



## 3. CONFIGURAÇÃO DO SERVIDOR

### 3.1 Editando server.properties

Após a primeira inicialização, o servidor cria o arquivo `server.properties`. Você pode editá-lo para personalizar o servidor:

```bash
nano server.properties
```

**Configurações importantes:**

```properties
server-name=Meu Servidor Android
gamemode=survival
difficulty=normal
max-players=10
view-distance=10
tick-distance=4
server-port=19132
server-portv6=19133
level-name=Bedrock level
```

**Dicas de otimização:**
- `view-distance=10` (reduza para 6-8 se tiver lag)
- `tick-distance=4` (mantenha baixo para economizar processamento)
- `max-players=10` (ajuste conforme sua RAM disponível)

Salve com `CTRL+X`, depois `Y` e `ENTER`.

### 3.2 Whitelist e Permissões

#### Ativar whitelist

Edite o `server.properties`:

```properties
white-list=true
```

Depois edite o arquivo `whitelist.json`:

```bash
nano whitelist.json
```

Adicione jogadores:

```json
[
  {
    "name": "NomeDoJogador1"
  },
  {
    "name": "NomeDoJogador2"
  }
]
```

#### Dar permissão de OP (admin)

Edite o arquivo `permissions.json`:

```bash
nano permissions.json
```

Adicione:

```json
[
  {
    "permission": "operator",
    "xuid": "123456789"
  }
]
```

Ou use o comando no console do servidor:

```
op NomeDoJogador
```

---

## 4. MANUTENÇÃO E BACKUPS

### 4.1 Fazendo Backup do Mundo

**Método 1: Manual (servidor parado)**

```bash
# Pare o servidor (CTRL+C na sessão do servidor)
cd ~/servidor_estavel
tar -czf backup_mundo_$(date +%Y%m%d_%H%M%S).tar.gz worlds/
```

**Método 2: Copiar para o celular**

```bash
cp -r worlds/ /sdcard/Download/backup_minecraft/
```

### 4.2 Restaurando Backup

```bash
cd ~/servidor_estavel
# Remova o mundo atual
rm -rf worlds/
# Extraia o backup
tar -xzf backup_mundo_20241126_153000.tar.gz
```

### 4.3 Atualizando o Servidor

1. Pare o servidor (CTRL+C)
2. Faça backup do mundo
3. Baixe a nova versão do Bedrock Server
4. Extraia sobrescrevendo os arquivos:

```bash
cd ~/servidor_estavel
unzip -o ~/server_novo.zip
```

5. **NÃO sobrescreva**: `server.properties`, `whitelist.json`, `permissions.json`, pasta `worlds/`
6. Reinicie o servidor



## 5. MONITORAMENTO E LOGS

### 5.1 Visualizando Logs

Os logs ficam em tempo real no terminal onde o servidor roda. Para salvar em arquivo:

```bash
./iniciar.sh 2>&1 | tee server.log
```

### 5.2 Monitorando Recursos

**Em outra sessão do Termux:**

```bash
# Instale htop
pkg install htop

# Execute
htop
```

Procure pelo processo `box64` para ver uso de CPU e RAM.

### 5.3 Verificando Temperatura (ROG Phone)

Use o app **Armoury Crate** ou **Game Genie** do ROG Phone para monitorar temperatura em tempo real.

**Temperaturas ideais:**
- ✅ 35-45°C: Excelente
- ⚠️ 45-55°C: Normal sob carga
- ❌ 55°C+: Reduza carga ou melhore ventilação



## 6. OTIMIZAÇÃO – ROG PHONE 5s

Para máxima performance e durabilidade:

- ✅ Ethernet na porta lateral USB-C
- ✅ Carregador na porta inferior
- ✅ Ativar **Bypass Charging** (Game Genie → Energia Direta)
- ✅ Reduz consumo e mantém temperatura baixa
- ✅ Bateria não descarrega nem carrega



## 7. TROUBLESHOOTING

### Problema: Servidor não inicia

**Causa 1: Porta já em uso**

```bash
# Verifique se algo está usando a porta 19132
netstat -tuln | grep 19132

# Se estiver ocupada, mate o processo ou mude a porta no server.properties
```

**Causa 2: Falta de memória**

```bash
# Verifique memória disponível
free -h

# Se estiver baixa, feche outros apps no Android
```

**Causa 3: Box64 não instalado corretamente**

```bash
# Reinstale o Box64
apt remove box64
apt update && apt install box64 -y
```

### Problema: Lag excessivo

**Soluções:**

1. Reduza `view-distance` no `server.properties` (ex: 6)
2. Reduza `max-players` (ex: 5-8 jogadores)
3. Use conexão Ethernet em vez de Wi-Fi
4. Feche apps em segundo plano no Android
5. Ative modo performance no ROG Phone

### Problema: Playit desconecta

**Soluções:**

1. Mantenha a tela do celular ligada (ou use app para evitar sleep)
2. Desative economia de bateria do Termux nas configurações do Android
3. Reinstale o agente Playit:

```bash
rm playit-linux-aarch64
wget https://github.com/playit-cloud/playit-agent/releases/latest/download/playit-linux-aarch64
chmod +x playit-linux-aarch64
./playit-linux-aarch64
```

### Problema: Mundo corrompido

**Solução:**

Restaure o último backup:

```bash
cd ~/servidor_estavel
rm -rf worlds/
tar -xzf backup_mundo_[DATA].tar.gz
```

### Problema: Jogadores não conseguem conectar

**Checklist:**

1. ✅ Servidor rodando? (Sessão 1)
2. ✅ Playit rodando? (Sessão 2)
3. ✅ Túnel configurado corretamente? (19132 UDP)
4. ✅ Whitelist desativada ou jogador adicionado?
5. ✅ IP correto do Playit? (verifique no site)



## 8. FAQ

### Posso usar outro celular além do ROG Phone?

Sim! Qualquer Android com Snapdragon 800+ e 6GB+ de RAM funciona. O ROG Phone é recomendado pela refrigeração e bypass charging.

### Quanto de internet consome?

Aproximadamente 10-50 MB/hora por jogador, dependendo da atividade.

### Posso deixar rodando 24/7?

Sim, desde que:
- Use bypass charging (ou mantenha plugado)
- Tenha boa ventilação
- Monitore temperatura regularmente

### Funciona com mods?

Não. O Bedrock Server oficial não suporta mods. Apenas addons e behavior packs oficiais.

### Posso rodar vários servidores?

Sim, mas cada um precisa de:
- Pasta separada
- Porta diferente no `server.properties`
- Túnel Playit separado



## 9. RESUMO

Este tutorial cobre:

- ✔ Servidor Local (LAN)
- ✔ Servidor Online com Playit
- ✔ Sessões simultâneas no Termux (parte crítica)
- ✔ Configuração completa do server.properties
- ✔ Sistema de backups e recuperação
- ✔ Monitoramento de recursos e temperatura
- ✔ Troubleshooting de problemas comuns
- ✔ Otimização do hardware do ROG Phone
- ✔ FAQ com dúvidas frequentes



## 📞 Suporte

Se tiver dúvidas ou problemas, verifique:

- Versão do Box64 está atualizada
- Ambas as sessões estão rodando (servidor + Playit)
- Porta 19132 está configurada corretamente no túnel
- Seção de [Troubleshooting](#7-troubleshooting) para problemas comuns



## 🎯 Dicas Finais

**Para melhor estabilidade:**
- Mantenha o Termux sempre aberto (não force close)
- Desative economia de bateria para o Termux nas configurações do Android
- Use Ethernet em vez de Wi-Fi sempre que possível
- Faça backups regulares (diariamente se possível)
- Monitore temperatura durante as primeiras horas

**Performance esperada:**
- 5-10 jogadores: Sem lag perceptível
- 10-15 jogadores: Possível lag leve em áreas densas
- 15+ jogadores: Necessário otimizar configurações



## 📝 Licença

Este é um guia educacional. Minecraft® é marca registrada da Mojang AB/Microsoft Corporation.



**Desenvolvido e testado com ASUS ROG Phone 5s**  
**Versão do servidor: Bedrock 1.21.124**  
**Box64 + Ubuntu 22.04 via Termux**
