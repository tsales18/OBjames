**IPC — Inter-Process Communication** — é o nome genérico para qualquer mecanismo que permite dois processos separados trocarem dados.

O problema que resolve é simples: cada processo no Windows vive em seu próprio espaço de memória isolado — o que existe dentro do driver `.exe` é invisível para a interface `.exe`. IPC é a ponte entre eles.

```
┌─────────────────┐         ┌─────────────────┐
│   driver.exe    │         │  interface.exe  │
│                 │         │                 │
│  catalogo[]     │ ──IPC── │  lê os dados    │
│  (memória C)    │         │  (memória C++)  │
│                 │         │                 │
└─────────────────└         └─────────────────┘
     processo 1                  processo 2
```

Os mecanismos existem porque processos isolados é um design intencional do sistema operacional — um processo não pode corromper a memória do outro, não pode acessar dados do outro sem permissão. IPC é o canal controlado que o SO oferece para quando dois processos precisam colaborar.

No Windows as formas principais são:

```
Shared Memory   → área de RAM compartilhada entre os dois
Named Pipe      → canal de bytes como um socket local
Socket TCP/UDP  → funciona até em máquinas diferentes
Arquivo         → um escreve, outro lê do disco
Message Queue   → fila de mensagens gerenciada pelo SO
COM/DCOM        → padrão Windows para objetos entre processos
```

