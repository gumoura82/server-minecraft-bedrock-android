# DOCUMENTAÇÃO TÉCNICA - SERVIDOR MINECRAFT BEDROCK NO ANDROID

Documentação explicando como funciona o servidor Minecraft Bedrock rodando em Android ARM64.

## Índice

1. [Como Funciona](#1-como-funciona)
2. [Por Que Usar Box64](#2-por-que-usar-box64)
3. [Configurações Usadas](#3-configurações-usadas)
4. [Troubleshooting Comum](#4-troubleshooting-comum)

## 1. Como Funciona

### O Problema

O servidor oficial do Minecraft Bedrock da Mojang só existe em versão x86_64 (Intel/AMD). Celulares Android usam processadores ARM64 (como Snapdragon). Não dá pra rodar direto.

### A Solução

Usamos uma "pilha" de programas que fazem o servidor funcionar:

```
Minecraft Bedrock Server (x86_64)
        ↓
    Box64 (traduz x86_64 → ARM64)
        ↓
    Ubuntu (ambiente Linux)
        ↓
    Termux (terminal Android)
        ↓
    Android (seu celular)
```

**Box64** é o programa que faz a "mágica" de traduzir as instruções do servidor x86_64 para ARM64 em tempo real.

### Como o Box64 Funciona

Box64 não é um emulador completo (tipo virtualização). Ele é um **tradutor de código**:

1. Pega as instruções x86_64 do servidor
2. Traduz para instruções ARM64
3. Guarda essas traduções em cache
4. Roda tudo no seu processador ARM

Por isso funciona razoavelmente rápido (não é tão lento quanto emulação completa).

## 2. Por Que Usar Box64

### Alternativas que NÃO funcionam:

❌ **Recompilar o servidor** - Não temos o código fonte do Bedrock Server  
❌ **QEMU** - Muito lento, emulação completa  
❌ **Versão Java** - É outro jogo diferente, não é compatível  

### Por que Box64 é a melhor opção:

✅ Usa o binário oficial da Mojang (sempre atualizado)  
✅ Não precisa de root  
✅ Performance aceitável para servidores pequenos  
✅ Atualizações do servidor funcionam automaticamente  

## 3. Configurações Usadas

### 3.1 Variáveis do Box64 no Script

No script `iniciar.sh`, usamos essas configurações:

```bash
export BOX64_DYNAREC_BIGBLOCK=0
export BOX64_DYNAREC_STRONGMEM=1
```

**O que cada uma faz:**

**BOX64_DYNAREC_BIGBLOCK=0**
- Controla o tamanho dos blocos de código traduzidos
- **0 = blocos pequenos** (mais estável, evita crashes)
- **1 = blocos médios** (mais rápido, mas pode crashar)
- **2-3 = blocos grandes** (muito rápido, mas instável)
- **Usamos 0** porque estabilidade é mais importante que velocidade

**BOX64_DYNAREC_STRONGMEM=1**
- Controla como a memória é acessada
- **0 = weak** (rápido, mas pode corromper dados)
- **1 = strong** (mais seguro, evita bugs)
- **2+ = very strong** (muito lento)
- **Usamos 1** para evitar corrupção de mundo

### 3.2 Por Que o Script Reinicia Automaticamente

O loop `while true` no script serve para:

```bash
while true
do
    box64 ./bedrock_server
    sleep 5
done
```

**Motivo:** Se o servidor crashar (e pode acontecer), ele reinicia sozinho após 5 segundos. Assim você não perde o servidor se der algum problema.

### 3.3 server.properties Recomendado

Essas são as configurações que afetam performance:

```properties
view-distance=10        # Distância que jogadores veem
tick-distance=4         # Distância onde o mundo "acontece"
max-players=10          # Máximo de jogadores
```

**Por que esses valores:**
- `view-distance=10`: Balanceado, não sobrecarrega
- `tick-distance=4`: Baixo para economizar CPU
- `max-players=10`: Seguro para 8GB RAM

Se seu celular tiver mais RAM, pode aumentar. Se estiver com lag, diminua.

## 4. Troubleshooting Comum

### Problema: Servidor crasha com "Segmentation Fault"

**Causa:** Box64 tentando traduzir um bloco muito grande de código

**Solução:** Já está configurado com `BIGBLOCK=0` no script. Se ainda crashar, pode ser falta de RAM.

### Problema: Servidor fica lento depois de horas rodando

**Causa:** Pode ser:
1. Temperatura alta (throttling)
2. RAM ficando cheia
3. Muitos chunks carregados

**Solução:**
1. Verifique temperatura (use app do fabricante)
2. Reinicie o servidor a cada 24-48h
3. Reduza `view-distance` no server.properties

### Problema: "Killed" aparece e servidor para

**Causa:** Falta de RAM (Android matou o processo)

**Solução:**
1. Feche outros apps
2. Reduza `max-players`
3. Reduza `view-distance`
4. Considere usar celular com mais RAM

### Problema: Termux fecha sozinho

**Causa:** Android está otimizando bateria e matando o Termux

**Solução:**
```
Configurações Android → Aplicativos → Termux → Bateria
→ Desmarque "Otimizar uso de bateria"
```

### Problema: Playit desconecta frequentemente

**Causa:** Termux entrando em sleep ou conexão Wi-Fi instável

**Solução:**
1. Desabilite economia de bateria do Termux
2. Use Ethernet em vez de Wi-Fi
3. Mantenha a tela ligada (ou use app wake lock)

## Limitações Conhecidas

### O que NÃO funciona:

❌ **Mods do Java Edition** - Bedrock não suporta  
❌ **Plugins Bukkit/Spigot** - São do Java Edition  
❌ **Root necessário** - Não precisa (usa proot)  
❌ **Rodar sem Playit** - Precisa de túnel ou port forwarding  

### O que funciona:

✅ **Addons oficiais do Bedrock** - Funcionam normalmente  
✅ **Behavior packs** - Funcionam normalmente  
✅ **Texture packs** - Funcionam normalmente  
✅ **Comandos do servidor** - Todos funcionam  
✅ **Whitelist/Permissions** - Funcionam normalmente  

## Informações Técnicas Adicionais

### Por que Ubuntu no Termux?

- Termux sozinho não tem todas as bibliotecas que o Bedrock Server precisa
- Ubuntu tem tudo pronto (libc, libstdc++, etc)
- proot-distro permite rodar Ubuntu sem root

### Por que Playit em vez de Port Forwarding?

**Port Forwarding (tradicional):**
- Precisa mexer no roteador
- IP muda (a menos que tenha IP fixo)
- Pode não funcionar em redes 4G/5G (CGNAT)

**Playit:**
- Funciona em qualquer rede
- Não precisa mexer no roteador
- IP permanente enquanto túnel existir
- Funciona até em rede universitária/trabalho

### Quanto de Performance se perde?

Não tenho números exatos de benchmarks, mas baseado na experiência com o ROG Phone 5s:

**Servidor vazio:** Roda leve, quase sem diferença  
**3-5 jogadores:** Roda bem, sem lag perceptível  
**10+ jogadores:** Começa a usar bastante CPU, mas funciona  

A "perda" de performance do Box64 existe, mas para servidores pequenos (amigos) não faz diferença prática.

### Observações sobre Chipsets

**Snapdragon (Qualcomm):**
- Testado com SD 888+ (ROG Phone 5s) - Funciona muito bem
- Outros Snapdragon 800+ devem funcionar similar
- Box64 tem bom suporte para ARM da Qualcomm

**Dimensity (MediaTek):**
- Deve funcionar (Box64 suporta ARM genérico)
- Pode esquentar mais que Snapdragon
- Não testado pessoalmente

**Exynos (Samsung):**
- Pode ter problemas de compatibilidade
- Alguns usuários reportam crashes
- Use com cautela

## Conclusão

Este setup funciona porque:

1. **Box64** traduz o servidor x86_64 para ARM64
2. **Ubuntu** fornece o ambiente Linux necessário
3. **Termux** roda tudo sem precisar de root
4. **Playit** resolve o problema de rede/portas
5. **Script anti-crash** mantém tudo estável

É uma solução "gambiarra técnica", mas funciona surpreendentemente bem para servidores pequenos de amigos (5-15 jogadores).
