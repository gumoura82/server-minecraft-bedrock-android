# SERVIDOR MINECRAFT BEDROCK NO ANDROID

![Testado](https://img.shields.io/badge/Status-Testado-success)
![Versão](https://img.shields.io/badge/Bedrock-1.21.124-blue)
![Android](https://img.shields.io/badge/Android-10+-green)

Guia para rodar o servidor oficial do Minecraft Bedrock em dispositivos Android usando Termux + Ubuntu + Box64.

## REQUISITOS

**Hardware Mínimo:**
- Processador: Snapdragon 800+ / Dimensity 1000+ / Exynos 2100+
- RAM: 6GB (8GB+ recomendado)
- Armazenamento: 5GB livres
- Android 10 ou superior

**Compatibilidade:**
- ✅ Snapdragon 800+: Melhor desempenho e compatibilidade
- ⚠️ Dimensity 1000+: Funcional, pode aquecer mais
- ⚠️ Exynos 2100+: Compatibilidade variável, teste antes

**Testado em:** ASUS ROG Phone 5s

**Verificar seu hardware:** Instale "CPU-Z" ou "DevCheck" para confirmar processador e RAM.

## ÍNDICE

1. [Servidor Local (LAN)](#1-servidor-local-lan)
2. [Servidor Online (Playit)](#2-servidor-online-playit)
3. [Configuração](#3-configuração)
4. [Backups](#4-backups)
5. [Monitoramento](#5-monitoramento)
6. [Troubleshooting](#6-troubleshooting)
7. [FAQ](#7-faq)

## 1. SERVIDOR LOCAL (LAN)

### 1.1 Instalar Termux

**F-Droid (recomendado):**
1. Acesse https://f-droid.org/
2. Baixe e instale o F-Droid
3. Procure por "Termux" e instale

**Play Store (alternativa):**
- Pode apresentar problemas, mas funciona

### 1.2 Configuração Inicial

Abra o Termux e execute os comandos abaixo. Basta copiar e colar cada linha.

```bash
pkg update && pkg upgrade
```

Quando perguntado `Do you want to continue? [Y/n]`, digite `Y` e pressione Enter.

### 1.3 Instalar proot-distro e Ubuntu

```bash
pkg install proot-distro
```

```bash
proot-distro install ubuntu
```

Aguarde o download (5-10 minutos dependendo da internet).

```bash
proot-distro login ubuntu
```

Você verá `root@localhost:~#` quando estiver dentro do Ubuntu.

### 1.4 Instalar Box64

Execute cada comando:

```bash
apt update && apt install wget gpg -y
```

```bash
wget https://ryanfortner.github.io/box64-debs/box64.list -O /etc/apt/sources.list.d/box64.list
```

```bash
wget -qO- https://ryanfortner.github.io/box64-debs/KEY.gpg | gpg --dearmor -o /etc/apt/trusted.gpg.d/box64.gpg
```

```bash
apt update && apt install box64 -y
```

### 1.5 Baixar Servidor Bedrock

**No navegador do celular:**
1. Acesse: https://www.minecraft.net/pt-br/download/server/bedrock
2. Baixe a versão Ubuntu (Linux)
3. O arquivo será baixado para a pasta Downloads do seu celular

**No Termux (dentro do Ubuntu):**

O arquivo baixado normalmente fica em um destes caminhos:
- `/sdcard/Download/` (maioria dos Androids)
- `/storage/emulated/0/Download/`
- `/sdcard/Downloads/` (alguns celulares usam "Downloads" com S)

Para facilitar, vamos listar o que tem na pasta:

```bash
ls /sdcard/Download/
```

Se aparecer "No such file or directory", tente:

```bash
ls /sdcard/Downloads/
```

Você verá algo como `bedrock-server-1.21.124.zip` (o número da versão pode variar).

Agora copie o arquivo (ajuste o caminho se necessário):

```bash
mkdir servidor_minecraft
cd servidor_minecraft
cp /sdcard/Download/bedrock-server-*.zip servidor.zip
```

**Se der erro**, tente com "Downloads" (com S):

```bash
cp /sdcard/Downloads/bedrock-server-*.zip servidor.zip
```

Depois extraia:

```bash
unzip -o servidor.zip
chmod +x bedrock_server
```

### 1.6 Script de Inicialização

Crie um script que reinicia automaticamente em caso de crash:

```bash
cat <<'EOF' > iniciar.sh
#!/bin/bash
while true; do
    echo "=========================================="
    echo " INICIANDO SERVIDOR MINECRAFT BEDROCK"
    echo " CTRL+C para parar"
    echo "=========================================="
    export BOX64_DYNAREC_BIGBLOCK=0
    export BOX64_DYNAREC_STRONGMEM=1
    LD_LIBRARY_PATH=. box64 ./bedrock_server
    echo "=========================================="
    echo " Servidor encerrado. Reiniciando em 5s..."
    echo "=========================================="
    sleep 5
done
EOF
chmod +x iniciar.sh
```

### 1.7 Iniciar Servidor

```bash
./iniciar.sh
```

Aguarde até ver `Server started.` nos logs.

### 1.8 Conectar (LAN)

**Descobrir IP do celular:**
1. Configurações → Wi-Fi → Toque na rede conectada
2. Anote o endereço IP (ex: `192.168.1.105`)

**No Minecraft Bedrock:**
1. Jogar → Servidores → Adicionar Servidor
2. Endereço: IP do celular (ex: `192.168.1.105`)
3. Porta: `19132`

## 2. SERVIDOR ONLINE (PLAYIT)

### 2.1 Entendendo Sessões do Termux

Para o servidor funcionar online, você precisa rodar **duas coisas simultaneamente**:
- Sessão 1: Servidor Minecraft
- Sessão 2: Playit (túnel)

**Criar nova sessão:**
- Arraste da esquerda para direita no Termux
- Toque em "New Session" ou "+"

**Alternar entre sessões:**
- Arraste novamente e selecione a sessão desejada

### 2.2 Criar Conta Playit

1. Acesse https://playit.gg
2. Crie uma conta (Sign Up)
3. Confirme o email
4. Faça login

### 2.3 Instalar Playit Agent

**Em uma nova sessão do Termux:**

```bash
cd ~
wget https://github.com/playit-cloud/playit-agent/releases/latest/download/playit-linux-aarch64
chmod +x playit-linux-aarch64
./playit-linux-aarch64
```

O terminal mostrará um link:
```
To link this agent visit:
https://playit.gg/claim/ABC123XYZ456
```

Copie e abra esse link no navegador.

### 2.4 Vincular Agente

1. Cole o link no navegador
2. A página detectará automaticamente o agente
3. Clique em "Claim Agent"
4. O terminal mostrará "Linked Successfully"

### 2.5 Criar Túnel

No site do Playit:
1. Vá em **Tunnels**
2. Clique em **Add Tunnel**
3. Configurações:
   - **Tunnel Type:** Minecraft Bedrock Edition
   - **Agent:** Selecione seu dispositivo
   - **Local Port:** 19132
4. Salve

O Playit gerará um endereço único (ex: `exemplo-123.gl.joinmc.link`)

**Importante:** Copie esse endereço. É o que você passará para outros jogadores.

### 2.6 Rodar Servidor + Playit Juntos

Neste ponto, você deve ter:
- **Sessão 1:** Servidor Minecraft rodando (desde a Parte 1)
- **Sessão 2:** Playit já rodando (desde a seção 2.3 quando você vinculou)

**Se ambos já estão rodando, você não precisa fazer nada!** O servidor já está online e acessível pelo endereço do Playit.

### Se precisar reiniciar algo:

**Servidor parou/travou:**
- Vá na sessão do servidor
- Execute novamente: `./iniciar.sh`
- O Playit continua funcionando normalmente

**Playit desconectou:**
- Vá na sessão do Playit
- Execute novamente: `./playit-linux-aarch64`
- O servidor continua funcionando normalmente

**Fechou o Termux por acidente:**
- Reabra o Termux
- Inicie o servidor em uma sessão
- Inicie o Playit em outra sessão

### ✅ Verificação:

Alterne entre as sessões (arraste da esquerda):
- **Sessão do servidor:** Logs do Minecraft aparecendo
- **Sessão do Playit:** Mostra "Tunnel connected"

**Ambas precisam estar ativas para o servidor funcionar online.**

### 2.7 Conectar

No Minecraft Bedrock:
1. Adicionar Servidor
2. Endereço: Cole o endereço do Playit
3. Porta: 19132 (ou deixe em branco)

## 3. CONFIGURAÇÃO

### 3.1 server.properties

Para o servidor (parado):

```bash
cd ~/servidor_minecraft
nano server.properties
```

**Principais configurações:**

```properties
server-name=Seu Servidor
gamemode=survival
difficulty=normal
max-players=10
view-distance=10
tick-distance=4
server-port=19132
level-name=Bedrock level
white-list=false
```

**Otimização:**
- `view-distance`: Reduza para 6-8 se tiver lag
- `tick-distance`: Mantenha em 3-4
- `max-players`: Ajuste conforme RAM (6GB = 5-8 jogadores, 8GB = 8-12)

Salvar: `CTRL+X` → `Y` → `Enter`

### 3.2 Whitelist

Para restringir acesso:

1. No `server.properties`: `white-list=true`
2. Edite `whitelist.json`:

```bash
nano whitelist.json
```

```json
[
  {"name": "Jogador1"},
  {"name": "Jogador2"}
]
```

### 3.3 Permissões OP

No console do servidor (rodando):
```
op NomeDoJogador
```

Ou edite `permissions.json`:
```json
[
  {
    "permission": "operator",
    "xuid": "123456789"
  }
]
```

## 4. BACKUPS

### 4.1 Backup Manual

Pare o servidor (`CTRL+C`) e execute:

```bash
cd ~/servidor_minecraft
tar -czf /sdcard/Download/backup_mundo_$(date +%Y%m%d_%H%M%S).tar.gz worlds/
```

### 4.2 Backup Rápido (sem compactar)

```bash
cp -r worlds/ /sdcard/Download/backup_minecraft_$(date +%Y%m%d)/
```

### 4.3 Restaurar Backup

```bash
cd ~/servidor_minecraft
rm -rf worlds/
tar -xzf /sdcard/Download/backup_mundo_DATA.tar.gz
```

Ou para backup não compactado:

```bash
cp -r /sdcard/Download/backup_minecraft_DATA/ worlds/
```

**Recomendação:** Faça backup antes de atualizar ou instalar addons.

## 5. MONITORAMENTO

### 5.1 Temperatura

**Por marca:**
- **Xiaomi/Poco:** Segurança → Bateria
- **Samsung:** Discar `*#0*#` ou usar Device Care
- **ASUS ROG:** Armoury Crate (tela inicial)
- **Qualquer:** Apps como CPU-Z, DevCheck, AIDA64

**Referência:**
- 30-40°C: Ideal
- 40-50°C: Normal sob carga
- 50-60°C: Monitore, melhore ventilação
- 60°C+: Pare o servidor

### 5.2 Recursos (CPU/RAM)

Em nova sessão do Termux:

```bash
pkg install htop
htop
```

Procure pelo processo `box64`. Pressione `Q` para sair.

### 5.3 Logs

Os logs aparecem em tempo real na sessão onde o servidor roda.

Para salvar em arquivo:

```bash
./iniciar.sh 2>&1 | tee server.log
```

## 6. TROUBLESHOOTING

### 6.1 Servidor não inicia

**Porta em uso:**
```bash
netstat -tuln | grep 19132
```

Se retornar algo, a porta está ocupada. Reinicie o celular ou mude a porta no `server.properties`.

**Box64 não instalado:**
```bash
apt remove box64
apt update && apt install box64 -y
```

**Memória insuficiente:**
Feche outros apps no Android.

### 6.2 Lag

**Soluções:**
- Reduza `view-distance` para 6-8
- Reduza `max-players`
- Use conexão Ethernet (adaptador USB-C → RJ45)
- Feche outros apps
- Ative modo performance no Android

### 6.3 Playit desconecta

**Desative economia de bateria para Termux:**

**Android genérico:**
Configurações → Aplicativos → Termux → Bateria → Sem restrições

**Xiaomi:**
Segurança → Bateria → Gerenciar uso → Termux → Sem restrições
Permissões → Autostart → Ativar

**Samsung:**
Configurações → Bateria → Aplicativos sem suspensão → Adicionar Termux

**OnePlus:**
Bateria → Otimização → Termux → Não otimizar

**Reinstalar Playit:**
```bash
rm playit-linux-aarch64
wget https://github.com/playit-cloud/playit-agent/releases/latest/download/playit-linux-aarch64
chmod +x playit-linux-aarch64
```

### 6.4 Termux fecha sozinho

Mesmo procedimento de desativar economia de bateria acima.

### 6.5 Conexão falha

**Checklist servidor local:**
- Servidor rodando?
- IP correto?
- Mesma rede Wi-Fi?
- Porta 19132?

**Checklist servidor online:**
- Servidor rodando (Sessão 1)?
- Playit rodando (Sessão 2)?
- Playit mostra "connected"?
- Endereço correto?
- Whitelist desativada ou jogador adicionado?

### 6.6 Mundo corrompido

Restaure o último backup (veja seção 4.3).

Se não houver backup, o mundo está perdido.

## 7. FAQ

## Funciona em qualquer Android?

Não. Requer processador compatível (Snapdragon 800+, Dimensity 1000+, Exynos 2100+), 6GB+ RAM e Android 10+.

## Precisa de root?

Não.

## É gratuito?

Sim. Tudo é gratuito.

## Posso rodar 24/7?

Sim, desde que:
- Mantenha plugado na tomada
- Tenha boa ventilação
- Monitore temperatura regularmente
- Desative economia de bateria para Termux

Celulares com bypass charging (ASUS ROG) são ideais para operação contínua.

## Quantos jogadores suporta?

Depende da RAM:
- 6GB: 5-8 jogadores
- 8GB: 8-12 jogadores
- 12GB+: 12-20 jogadores

## Consome muita bateria?

Sim. Mantenha sempre plugado.

## Funciona com mods?

Não suporta mods do Java Edition. Aceita addons, behavior packs e resource packs do Bedrock.

## Wi-Fi ou Ethernet?

Ethernet é superior (estabilidade, latência). Use adaptador USB-C → RJ45 (~R$30-80).

## Quanto de dados consome?

Aproximadamente 10-50 MB/hora por jogador.

## Como atualizar?

1. Faça backup do mundo
2. Pare o servidor
3. Baixe nova versão do Bedrock Server
4. Extraia sobrescrevendo: `unzip -o bedrock-server-NOVO.zip`
5. **Não sobrescreva:** `server.properties`, `whitelist.json`, `permissions.json`, pasta `worlds/`
6. Reinicie

## Funciona em iOS?

Não. Apenas Android.

## Melhores celulares para servidor?

**Budget:** Poco F5, Realme GT Neo 3
**Intermediário:** Xiaomi 13T, OnePlus 11R
**Premium:** ASUS ROG Phone 6/7 (ideal para 24/7), OnePlus 12

Priorize: Snapdragon 800+, 8GB+ RAM, boa refrigeração.

## OTIMIZAÇÃO TÉRMICA

### Geral

- Remova capas
- Mantenha em local ventilado
- Reduza brilho da tela
- Feche apps em segundo plano
- Use Ethernet em vez de Wi-Fi

### Por marca

**ASUS ROG Phone:**
- Ative Bypass Charging (Game Genie)
- Use AeroActive Cooler se disponível
- Modo X no Armoury Crate

**Xiaomi/Poco:**
- Modo performance (Bateria)
- Game Turbo
- Desative MIUI Optimization para Termux

**Samsung:**
- Enhanced Processing (Opções de desenvolvedor)
- Game Booster → Performance

**OnePlus:**
- Gaming Mode → Performance

### Acessórios

- Mini ventilador USB (~R$20-50)
- Suporte com ventilação
- Dissipador de cobre/alumínio (~R$15-30)

## RESUMO TÉCNICO

**Stack:**
- Termux (emulador de terminal Android)
- proot-distro (distribuições Linux no Termux)
- Ubuntu 22.04 (ambiente Linux)
- Box64 (emulador x86_64 para ARM64)
- Bedrock Server oficial (Mojang)
- Playit.gg (túnel de rede opcional)

**Fluxo:**
1. Termux fornece ambiente shell
2. proot-distro roda Ubuntu sem root
3. Box64 traduz binário x86_64 do servidor para ARM64
4. Playit cria túnel UDP para acesso externo

**Limitações:**
- Não roda mods Java
- Performance depende do hardware
- Aquecimento pode ser limitante
- Playit gratuito tem limites de túneis

## CRÉDITOS

**Testado em:** ASUS ROG Phone 5s  
**Versão do servidor:** Bedrock 1.21.124  
**Box64:** https://github.com/ptitSeb/box64  
**Playit:** https://playit.gg  

Minecraft® é marca registrada da Mojang AB/Microsoft Corporation.

**Última atualização:** Fevereiro 2026
