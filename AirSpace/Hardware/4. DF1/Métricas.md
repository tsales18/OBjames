# 1) Confiabilidade de Transação

- [ ] **1.1 Taxa de sucesso por transação**

- **O que é:** % de requisições que retornam resposta válida (STS=0, BCC OK) sem esgotar retries.
- **Alvo:** ≥ **99,99%** em **100.000 leituras** contínuas (bancada estável).
- **Medida:** contador `ok/total` por janela (ex.: 1.000 / 10.000 / 100.000).

- [ ] **1.2 Taxa de erro por tipo**

 - **O que é:** distribuição de falhas entre **Timeout**, **NAK**, **STS≠0**, **BCC inválido**, **porta indisponível**.
- **Alvo:**
    - Timeout ≤ **0,20%**
    - NAK ≤ **0,10%**
    - BCC inválido (**recebido**) ≤ **0,05%** e **sempre** recuperado via retry ≤2
    - STS≠0 reportado 100% com código decodificado
    
- **Medida:** contadores por tipo, por janela.
    

- [ ] **1.3 Eficácia de retry**

- **O que é:** % de falhas recuperadas **após retry** dentro do orçamento de tentativas.
    
- **Alvo:** ≥ **99,9%** das falhas recuperáveis resolvidas em ≤ **2 retries** com backoff exponencial.
    
- **Medida:** `recuperadas/total_falhas_recuperáveis`.
    

- [ ] **1.4 Integridade TNS**

- **O que é:** **duplicatas ou fora de ordem** de TNS nas respostas.
    
- **Alvo:** **0 ocorrências** em 100.000 transações.
    
- **Medida:** verificação sequencial, log de anomalias.
    

---

# 2) Desempenho e Determinismo

- [ ] **2.1 RTT (Round-Trip Time)**

- **O que é:** tempo entre envio e recebimento da resposta.
    
- **Alvos em 19.2 kbps, cabo ≤ 15 m, PC comum:**
    
    - **média** < **12 ms**
        
    - **p95** < **25 ms**
        
    - **p99** < **40 ms**
        
- **Medida:** histograma por janela, média/p95/p99.
    

- [ ] **2.2 Throughput estável**

- **O que é:** leituras/s sustentadas sem romper p95.
    
- **Alvo:** **10 Hz** por tag (ou **64 tags** em varredura a **1 Hz**) mantendo p95 acima; documentar limites.
    
- **Medida:** taxa efetiva vs. latência.
    

- [ ] **2.3 Drift de scan (determinismo)**

- **O que é:** desvio entre o agendamento desejado e o timestamp real da leitura.
    
- **Alvo:** **drift p95 ≤ 50 ms** por 10 min de operação.
    
- **Medida:** `|t_real - t_agendado|` por leitura.
    

- [ ] **2.4 Consumo de recursos**

- **CPU:** ≤ **5%** num i5/8GB em 64 tags @1 Hz.
    
- **Memória:** drift ≤ **1 MB/h** em teste de 8 h (sem vazamento).
    
- **Medida:** amostragem periódica + gráfico.
    

---

# 3) Robustez e Recuperação

- [ ] **3.1 Reconexão (hot unplug/replug)**

- **O que é:** tempo da detecção de link down até a retomada de leituras estáveis.
- **Alvo:** **< 3 s** para detectar e **< 5 s** para retomar.
- **Medida:** rote o cabo 10x; meça.

- [ ] **3.2 Circuit breaker**

- **O que é:** entrada/saída de estado “degradado” após N falhas; política de backoff.
- **Alvo:** entra após **N** falhas consecutivas (definido) e tenta **recovery** com backoff; sem loop infinito
- **Medida:** transições de estado, tempos.

**3.3 Tolerância a frames corrompidos/truncados**

- **O que é:** capacidade de descartar parcial, re-sincronizar no próximo **DLE STX** e seguir.
- **Alvo:** **0 crashes, 0 deadlocks** em 1h com injeção de erros.
- **Medida:** campanha de erros sintéticos.

**3.4 DLE stuffing**

- **O que é:** tratamento correto de **0x10 0x10** no payload.
- **Alvo:** **0 erros** em 10.000 frames contendo DLE no payload.
- **Medida:** testes com dados artificiais contendo DLE.
    

---

# 4) Correção Funcional

**4.1 Cobertura de tipos e tamanhos**

- **O que é:** leitura correta de **B** (1 byte), **N** (INT16, 2 bytes), **F** (REAL, 4 bytes), inclusive **blocos contíguos**.
- **Alvo:** 100% OK nos testes: unitário e bloco (ex.: N7:0..N7:9).
- **Medida:** comparação com valores conhecidos no CLP.
    

**4.2 Escrita segura (quando habilitar)**

- **Whitelist:** 100% de **bloqueio** de tentativa fora da lista.
    
- **Read-back:** **100%** das escritas confirmadas por leitura subsequente; mismatch = erro.
    
- **Medida:** matriz de writes válidos/invalidos.
    

**4.3 Decodificação de STS**

- **O que é:** mapear códigos de status em mensagens claras.
    
- **Alvo:** **100%** dos STS que podem ocorrer mapeados e logados.
    
- **Medida:** tabela + testes com endereços inválidos e condições simuladas.
    

---

# 5) Observabilidade e Diagnóstico

**5.1 Log por transação (completo)**

- **O que é:** cada transação com: timestamp ISO, TNS, CMD, bytes TX/RX em hex, RTT, resultado, retries.
    
- **Alvo:** **100%** das transações logadas nesse formato quando `trace=on`.
    
- **Medida:** verificação de amostras e rotação de logs.
    

**5.2 Métricas “prontas pra dashboard”**

- **O que é:** counters/gauges expostos (JSON/CLI) para: sucesso%, erros por tipo, RTT média/p95/p99, retries, reconexões, estado do link.
    
- **Alvo:** endpoint/CLI responde em **< 100 ms**; sem travar loop de I/O.
    
- **Medida:** chamadas periódicas.
    

**5.3 Correlacionabilidade**

- **O que é:** capacidade de cruzar log com eventos de campo.
    
- **Alvo:** timestamps monotônicos, timezone explícito, precisão de **ms**.
    
- **Medida:** validação com NTP ou clock do SO.
    

---

# 6) Configurabilidade e Operação

**6.1 Config externa**

- **O que é:** porta, baud, timeouts, retries, lista de tags, whitelist, scan groups.
    
- **Alvo:** 100% carregado de **YAML/JSON**; erro de validação **antes** de iniciar I/O.
    
- **Medida:** teste com configs válidas/ inválidas.
    

**6.2 Reload sem restart**

- **O que é:** recarregar config em runtime.
    
- **Alvo:** **MTTR < 2 s**, nenhuma perda de estabilidade.
    
- **Medida:** alternar configs e medir.
    

**6.3 CLI de campo**

- **O que é:** comandos mínimos para manutenção: `read`, `write` (quando liberado), `metrics`, `trace on/off`, `health`.
    
- **Alvo:** todos retornam **0** em caso de sucesso e **códigos claros** em erro.
    
- **Medida:** suíte de CLI.
    

---

# 7) Compatibilidade e Ambiente

**7.1 Intra-modelo (que você já começou)**

- **O que é:** 3 unidades do mesmo modelo com configs distintas (baud, cabo, fonte).
    
- **Alvo:** **0 regressões** mudando baud 9600/19200/38400; cabos 1 m e 15 m; adaptador USB↔RS232.
    
- **Medida:** matriz de testes.
    

**7.2 Cross-modelo**

- **O que é:** pelo menos **1 outro modelo** da família (ex.: SLC-5/04 + MicroLogix 1200).
    
- **Alvo:** **100%** dos casos básicos OK; documentar diferenças/limites.
    
- **Medida:** mesma suíte.
    

**7.3 Ruído/ambiente**

- **O que é:** teste com máquina operando (EMI), aterramento real, ventiladores/inversores ligados.
    
- **Alvo:** manter métricas de confiabilidade com, no máximo, **+50%** de aumento temporário em timeout/NAK vs. bancada.
    
- **Medida:** repetir 10.000 leituras em ambiente ruidoso.
    

---

# 8) Ensaios de Longevidade

**8.1 Soak test 1h**

- **O que é:** 1 hora de leitura contínua (10 Hz em 1 tag ou 1 Hz em 64 tags).
    
- **Alvo:** **0 crashes**, **0 deadlocks**, drift de memória ≤ **1 MB/h**, RTT p95 dentro dos limites.
    
- **Medida:** logs + profiler leve.
    

**8.2 Endurance 8h (noturno)**

- **O que é:** teste prolongado com rotação de logs.
    
- **Alvo:** **estabilidade total**, rotação sem perda, reconexão automática se houver microquedas.
    
- **Medida:** relatório de início/fim com métricas agregadas.
    

---

# 9) Segurança Operacional

**9.1 Modo leitura por padrão**

- **O que é:** o driver sobe sem permissão de escrita.
    
- **Alvo:** 100% das execuções partem em **read-only**.
    
- **Medida:** checagem de startup.
    

**9.2 Whitelist de escrita**

- **O que é:** apenas tags explicitamente liberadas podem ser escritas.
    
- **Alvo:** **0** escritas fora da whitelist; 100% logadas.
    
- **Medida:** testes com tentativas inválidas.
    

**9.3 Read-back obrigatório**

- **O que é:** após escrever, ler e comparar.
    
- **Alvo:** **100%** das escritas confirmadas; mismatch = erro bloqueante.
    
- **Medida:** suíte de write.
    

---

# 10) Critérios de “Pronto” (DF1 v1.0)

- Sucesso global ≥ **99,99%** em 100.000 leituras.
    
- RTT média/p95/p99 dentro dos **limites** definidos.
    
- Reconexão **< 5 s** total; sem travas.
    
- TNS sem duplicata/fora de ordem.
    
- DLE stuffing validado.
    
- Timeout/NAK/BCC tratados com retry ≤2 e **sem deadlock**.
    
- Soak 1h e endurance 8h **verdes**.
    
- Logs e métricas completas.
    
- README com **Contrato de Comportamento**, **Matriz de Testes** e **Limites Conhecidos** publicado.