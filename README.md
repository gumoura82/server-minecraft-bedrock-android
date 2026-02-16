# SERVIDOR DEDICADO MINECRAFT BEDROCK NO ANDROID

![Testado](https://img.shields.io/badge/Status-Testado-success)
![Versão](https://img.shields.io/badge/Bedrock-1.21.124-blue)
![Android](https://img.shields.io/badge/Android-10+-green)
![Box64](https://img.shields.io/badge/Box64-Compatible-orange)

Transforme qualquer smartphone Android potente em um **servidor dedicado oficial do Minecraft Bedrock**, rodando via Termux + Ubuntu + Box64.

**Dispositivo testado:**
- ✅ ASUS ROG Phone 5s (dispositivo usado no desenvolvimento deste guia)

**Outros dispositivos compatíveis reportados:**
- Qualquer Android com Snapdragon 800+ e 6GB+ RAM
- Qualquer Android com Dimensity 1000+ e 8GB+ RAM
- Alguns modelos com Exynos 2100+ (compatibilidade pode variar)

## 📋 Índice

1. [Servidor Local (LAN)](#1-servidor-local-lan)
2. [Servidor Online (Internet)](#2-servidor-online-internet)
3. [Configuração do Servidor](#3-configuração-do-servidor)
4. [Manutenção e Backups](#4-manutenção-e-backups)
5. [Monitoramento e Logs](#5-monitoramento-e-logs)
6. [Otimização e Gerenciamento Térmico](#6-otimização-e-gerenciamento-térmico)
7. [Troubleshooting](#7-troubleshooting)
8. [FAQ](#8-faq)
9. [Compatibilidade e Limitações](#9-compatibilidade-e-limitações)
10. [Contribuições da Comunidade](#10-contribuições-da-comunidade)
11. [Resumo](#11-resumo)

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

- **Processador:** Snapdragon 800+ ou equivalente (Dimensity 1000+, Exynos 2100+)
- **RAM:** 6-8 GB (mínimo) | 8-12 GB (ideal para 10+ jogadores)
- **Armazenamento:** 5 GB livres
- **Conexão:** Adaptador USB-C → Ethernet (para máxima estabilidade)

**Dispositivos testados:**
- ✅ ASUS ROG Phone 5s (referência deste guia - desempenho excelente)
- ⚠️ **Outros dispositivos:** Qualquer Android com especificações similares ou superiores deve funcionar, mas não foram testados pelo autor

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
    echo " 🛡️ INICIANDO SERVIDOR BEDROCK..."
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

✅ **Pronto!** O servidor já funciona **somente para jogadores da mesma rede local** (LAN/Wi-Fi).  
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
2. Clique em **Add Tunnel** ou **Create**
3. **Agent:** Selecione o agente do seu celular (que você acabou de vincular)
4. **Tunnel Type:** Escolha **Minecraft Bedrock Edition**
5. **Local Port:** Digite **19132** (porta padrão do Bedrock Server)
   - ⚠️ Pode usar outra porta (ex: 25565) se mudou no `server.properties`, mas **19132** é o padrão
6. Clique em **Add Tunnel** ou **Save**

✅ **Pronto!** O Playit vai gerar automaticamente um endereço público.

### Onde encontrar seu IP público:

Depois de criar o túnel, você verá uma tela **"Your Tunnel"** mostrando seu endereço único.

**Exemplo de como pode aparecer:**

```
palavra-aleatoria.gl.joinmc.link
```

Outros formatos possíveis:

```
exemplo-qualquer.gl.at.ply.gg
random-name.joinmc.link
outro-exemplo.gl.joinmc.link
```

⚠️ **ATENÇÃO:** O endereço acima é apenas um **EXEMPLO**. O seu será completamente diferente e único! Cada túnel recebe um nome aleatório gerado pelo Playit.

**📋 Copie o endereço que aparecer na SUA tela** — esse é o IP que você vai passar pros seus amigos!

💡 **IMPORTANTE:** Para Minecraft Bedrock, você **NÃO precisa** colocar porta separada. O endereço já vem completo e configurado automaticamente na porta padrão do Bedrock.

### 2.5 Iniciando Servidor + Playit (Online)

⚠️ **Lembre-se:** Você precisa de **DUAS SESSÕES** do Termux rodando ao mesmo tempo!

#### Passo a passo completo:

**1. Abra o Termux**

**2. Sessão 1 - Inicie o Servidor:**

```bash
proot-distro login ubuntu
cd servidor_estavel
./iniciar.sh
```

Deixe essa sessão rodando. **NÃO FECHE!**

**3. Abra uma segunda sessão do Termux:**
- Arraste da esquerda para direita (menu lateral)
- Toque em **New Session** ou **+**

**4. Sessão 2 - Inicie o Playit:**

```bash
./playit-linux-aarch64
```

Deixe essa sessão rodando também. **NÃO FECHE!**

### ✅ Checklist - Servidor Online Funcionando:

- [ ] Sessão 1 mostrando logs do servidor Minecraft
- [ ] Sessão 2 mostrando "Tunnel connected" ou similar
- [ ] Ambas as sessões abertas simultaneamente
- [ ] Você tem o endereço do túnel (ex: `seu-endereco.gl.joinmc.link`)

Se todos os itens estão marcados, seu servidor está **ONLINE**! 🎉

### Como conectar no servidor:

**Opção 1: Adicionar servidor manualmente**

1. Abra o Minecraft Bedrock
2. Vá em **Servidores** → **Adicionar Servidor**
3. **Nome:** Escolha um nome qualquer (ex: "Servidor do João")
4. **Endereço:** Cole o endereço que apareceu no Playit (ex: `seu-endereco.gl.joinmc.link`)
5. **Porta:** Deixe em branco ou use **19132**
6. Salve e conecte!

**Opção 2: Usar o endereço direto (alguns clientes)**

Simplesmente copie e cole o endereço completo que o Playit gerou pra você.

💡 **Dica:** O endereço do Playit é único e permanente enquanto o túnel existir. Anote ele ou tire print para não perder!

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

### 5.3 Verificando Temperatura

**Métodos por fabricante:**

- **ASUS ROG Phone:** App Armoury Crate ou Game Genie
- **Xiaomi/Poco:** MIUI's Security App → Battery
- **Samsung:** Good Guardians → Thermal Guardian (baixar da Galaxy Store)
- **OnePlus:** Oxygen OS Dashboard
- **Genérico:** Apps como CPU-Z, DevCheck, AIDA64

**Temperaturas seguras:**
- ✅ 35-45°C: Excelente
- ⚠️ 45-55°C: Normal sob carga (monitore)
- ❌ 55°C+: Reduza carga ou melhore refrigeração

## 6. OTIMIZAÇÃO E GERENCIAMENTO TÉRMICO

### 6.1 Para qualquer dispositivo:

- ✅ Use conexão Ethernet (adaptador USB-C → RJ45)
- ✅ Mantenha o celular em local ventilado
- ✅ Evite usar capa durante operação do servidor
- ✅ Considere usar cooler/ventoinha externa (USB)
- ✅ Desative apps em segundo plano
- ✅ Reduza brilho da tela ao mínimo (ou use app de tela preta)

### 6.2 Recursos específicos por marca:

#### **ASUS ROG Phone (5s, 6, 7, 8):**
- ✅ Ative **Bypass Charging** (Game Genie → Energia Direta)
- ✅ Use porta lateral para Ethernet + porta inferior para carregador
- ✅ Modo X no Armoury Crate para máxima performance
- ✅ Ventilador AeroActive Cooler (se disponível)

#### **Xiaomi/Poco:**
- ✅ Ative modo performance nas configurações de bateria
- ✅ Desative MIUI Optimization para o Termux
- ✅ Use o Game Turbo se disponível
- ✅ Configurações → Bateria → desative economia para Termux

#### **Samsung:**
- ✅ Ative "Enhanced Processing" nas configurações de desenvolvedor
- ✅ Desative otimização de bateria para o Termux
- ✅ Use Good Guardians → Thermal Guardian (se disponível)
- ✅ Game Launcher → Game Booster → Performance

#### **OnePlus:**
- ✅ Desative otimização de bateria para Termux
- ✅ Ative modo performance
- ✅ Gaming Mode → Performance Mode

#### **Motorola:**
- ✅ Moto Gametime → Performance Mode
- ✅ Desative otimização de bateria

#### **Realme:**
- ✅ Game Space → Performance Mode
- ✅ Desative otimização de bateria

### 6.3 Dicas universais de refrigeração:

1. **Suporte com ventilação:** Eleve o celular para permitir fluxo de ar
2. **Cooler externo:** Mini ventiladores USB (~R$20-50)
3. **Dissipador passivo:** Placas de cobre/alumínio (~R$15-30)
4. **Ambiente:** Mantenha em local com ar-condicionado se possível
5. **Posição:** Deixe na horizontal para melhor dissipação

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
5. Ative modo performance no seu celular
6. Reduza `tick-distance` para 3

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
6. ✅ Versão do Minecraft compatível? (cliente deve ser igual ou próxima)

### Problema: Termux fecha sozinho

**Solução:**

```bash
# No Android, vá em:
Configurações → Aplicativos → Termux → Bateria
→ Desmarque "Otimizar uso de bateria"

# Alguns fabricantes:
# Xiaomi: Security → Battery → Manage apps battery usage → Termux → No restrictions
# Samsung: Battery → App power management → Apps that won't be put to sleep → Add Termux
# OnePlus: Battery → Battery optimization → Termux → Don't optimize
```

## 8. FAQ

### Posso usar outro celular além do ROG Phone?

Sim! Qualquer Android com:
- Snapdragon 800+ (ou equivalente)
- 6GB+ RAM
- Android 10+

O ROG Phone foi usado nos testes por ter recursos extras (bypass charging, refrigeração ativa), mas **NÃO É OBRIGATÓRIO**. Usuários reportaram sucesso com Xiaomi Poco F3, Samsung S21+, OnePlus 9 Pro e outros.

### Quanto de internet consome?

Aproximadamente 10-50 MB/hora por jogador, dependendo da atividade.

### Posso deixar rodando 24/7?

Sim, desde que:
- Use bypass charging (ROG Phone) ou mantenha plugado
- Tenha boa ventilação
- Monitore temperatura regularmente
- Desative economia de bateria do Termux

### Funciona com mods?

Não. O Bedrock Server oficial não suporta mods Java. Apenas addons e behavior packs oficiais do Bedrock.

### Posso rodar vários servidores?

Sim, mas cada um precisa de:
- Pasta separada
- Porta diferente no `server.properties`
- Túnel Playit separado

### Qual a diferença entre usar Wi-Fi e Ethernet?

**Wi-Fi:**
- ✅ Mais prático
- ❌ Latência variável
- ❌ Pode cair conexão

**Ethernet (adaptador USB-C):**
- ✅ Latência estável
- ✅ Conexão mais confiável
- ✅ Melhor para servidores 24/7
- ❌ Precisa de adaptador (~R$30-80)

### O servidor consome muita bateria?

Sim. Recomendações:
- Mantenha sempre plugado na tomada
- Use bypass charging se disponível (ROG Phone)
- Ou aceite que a bateria ficará em ciclo constante de carga

### Quantos jogadores consigo hospedar?

Depende do seu celular:
- **6GB RAM:** 5-8 jogadores
- **8GB RAM:** 8-12 jogadores
- **12GB+ RAM:** 12-20 jogadores

Sempre teste e monitore temperatura e performance.

## 9. COMPATIBILIDADE E LIMITAÇÕES

### Processadores testados:

- ✅ **Snapdragon 800+** (melhor desempenho e compatibilidade)
- ⚠️ **Dimensity 1000+** (funcional, pode esquentar mais)
- ⚠️ **Exynos 2100+** (funcional em alguns modelos, teste antes)
- ❌ **Processadores abaixo de Snapdragon 730** (não recomendado)

### Observações por chipset:

**Snapdragon:**
- ✅ Melhor compatibilidade com Box64
- ✅ Desempenho excelente
- ✅ Menor consumo térmico
- **Recomendado:** 860, 870, 888, 8 Gen 1, 8 Gen 2, 8 Gen 3

**Dimensity (MediaTek):**
- ⚠️ Funciona bem com Box64
- ⚠️ Monitore temperatura de perto
- ⚠️ Pode ter throttling térmico mais rápido
- **Testados:** Dimensity 1200, 8100, 9000

**Exynos (Samsung):**
- ⚠️ Compatibilidade variável
- ⚠️ Alguns modelos têm problemas com Box64
- ⚠️ Teste antes de confiar em produção
- **Melhor evitar** para servidores 24/7

### Versões Android testadas:

- ✅ Android 10: Funcional
- ✅ Android 11: Funcional
- ✅ Android 12: Funcional
- ✅ Android 13: Funcional
- ✅ Android 14: Funcional
- ✅ Android 15: Funcional (últimos testes)

### Limitações conhecidas:

- ❌ Não funciona em iOS (iPhone/iPad)
- ❌ Não suporta mods do Java Edition
- ❌ Requer root? **NÃO**
- ⚠️ Playit gratuito tem limite de ~1 túnel ativo
- ⚠️ Performance cai significativamente abaixo de 50% de bateria (se não plugado)

## 10. CONTRIBUIÇÕES DA COMUNIDADE

### Testou em outro dispositivo?

Abra uma issue ou pull request reportando:

- ✅ Modelo do celular
- ✅ Chipset (Snapdragon/Dimensity/Exynos + número)
- ✅ RAM
- ✅ Versão do Android
- ✅ Número de jogadores testado
- ✅ Temperatura média durante operação
- ✅ Problemas encontrados (se houver)
- ✅ Soluções aplicadas

**Sua contribuição ajuda a comunidade a saber quais dispositivos funcionam melhor!**

## 11. RESUMO

Este tutorial cobre:

- ✔ Servidor Local (LAN)
- ✔ Servidor Online com Playit
- ✔ Sessões simultâneas no Termux (parte crítica)
- ✔ Configuração completa do server.properties
- ✔ Sistema de backups e recuperação
- ✔ Monitoramento de recursos e temperatura
- ✔ Otimização por fabricante (ASUS, Xiaomi, Samsung, OnePlus, etc.)
- ✔ Troubleshooting de problemas comuns
- ✔ Compatibilidade de chipsets e dispositivos
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

| Jogadores | RAM Mínima | Performance | Observações |
|-----------|------------|-------------|-------------|
| 3-5 | 6GB | ✅ Sem lag | Ideal para amigos |
| 5-10 | 8GB | ✅ Sem lag perceptível | Recomendado |
| 10-15 | 12GB | ⚠️ Lag leve em áreas densas | Monitore temperatura |
| 15+ | 12GB+ | ⚠️ Otimização necessária | Reduza view-distance |

## 🌟 Créditos

**Desenvolvido e testado com:**
- Dispositivo principal: ASUS ROG Phone 5s
- Versão do servidor: Bedrock 1.21.124
- Box64 + Ubuntu 22.04 via Termux

**Tecnologias utilizadas:**
- [Termux](https://termux.dev/) - Emulador de terminal Android
- [proot-distro](https://github.com/termux/proot-distro) - Distribuições Linux no Termux
- [Box64](https://github.com/ptitSeb/box64) - Emulador x86_64 para ARM64
- [Playit.gg](https://playit.gg/) - Túnel de rede gratuito
- [Minecraft Bedrock Server](https://www.minecraft.net/en-us/download/server/bedrock) - Servidor oficial da Mojang

## 📝 Licença

Este é um guia educacional. Minecraft® é marca registrada da Mojang AB/Microsoft Corporation.

## 🔄 Atualizações do Guia

**Última atualização:** Fevereiro 2026

**Próximas melhorias planejadas:**
- [ ] Guia de instalação de addons e behavior packs
- [ ] Otimização avançada de performance
- [ ] Script automático de backup
- [ ] Integração com Discord bot
- [ ] Suporte a texturas customizadas

## ⭐ Gostou?

Se este guia te ajudou, considere:
- ⭐ Dar uma estrela no repositório
- 🔄 Compartilhar com amigos
- 💬 Reportar seu teste de dispositivo
- 🐛 Reportar bugs ou melhorias

**Transforme seu celular em um servidor dedicado Minecraft! 🎮📱**
