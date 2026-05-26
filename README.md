# Simulador de Escalonamento Preemptivo de Processos

Este repositório contém a implementação do Primeiro Trabalho (T1) da disciplina de Sistemas Operacionais (INF1316).

## 1. Proposta do Trabalho

O objetivo principal deste projeto foi desenvolver, na linguagem C, um simulador de núcleo de sistema operacional (KernelSim) capaz de gerenciar a execução concorrente de cinco processos de aplicação (A1 a A5). 

Para alcançar este objetivo, o trabalho exigiu a aplicação prática de conceitos fundamentais de sistemas operacionais em ambiente Unix/Linux, incluindo:
* Criação dinâmica de processos utilizando as chamadas de sistema `fork()` e famílias `exec()`.
* Comunicação Inter-Processos (IPC) por meio da estrutura de `pipes`.
* Controle de execução e mudança de estados de processos através do envio de sinais POSIX (`SIGSTOP`, `SIGCONT` e `SIGTSTP`).
* Implementação de um algoritmo de escalonamento preemptivo baseado em tempo (Round-Robin) e eventos de entrada e saída (E/S).

## 2. Implementação e Arquitetura do Sistema

A solução foi estruturada de forma modular, separando as responsabilidades do sistema operacional (Kernel), do hardware (Controlador de Interrupções) e das aplicações do usuário. 

A arquitetura final é composta pelos seguintes módulos principais:

* **KernelSim (`kernel.c`)**: Atua como o núcleo do sistema operacional. É responsável por instanciar os processos de aplicação e implementar o escalonador Round-Robin. O Kernel escuta continuamente um `pipe` em busca de eventos. Ele altera o estado dos processos (Pronto, Executando, Bloqueado, Terminado) através de sinais, retirando a CPU de um processo quando sua fatia de tempo acaba ou quando ele solicita uma operação de disco.
* **InterController Sim (`controller.c`)**: Emula o hardware do sistema. Executa paralelamente ao Kernel e é responsável por gerar interrupções assíncronas que são enviadas via `pipe`. Dispara a interrupção temporal `IRQ0` a cada 500ms (forçando a preempção) e sorteia o término de operações de leitura/escrita nos dispositivos virtuais D1 e D2 (`IRQ1` e `IRQ2`).
* **Processos de Aplicação (`app.c`)**: Representam os programas em espaço de usuário. Executam ciclos simulados e informam constantemente seu estado (Program Counter e memória acessada) ao Kernel. Ocasionalmente, disparam uma `SYSCALL` solicitando acesso a um disco, o que causa o seu bloqueio imediato até que o hardware finalize a operação.
* **Bootstrap (`main.c`)**: Ponto de entrada do sistema. Responsável por inicializar os canais de comunicação (`pipes`), realizar o `fork()` inicial que divide a execução entre Kernel e Controlador, e gerenciar o encerramento seguro do ambiente.

## 3. Instruções de Compilação e Uso

O projeto utiliza um arquivo `Makefile` para automatizar o processo de compilação e separar os códigos-fonte dos arquivos binários resultantes.

### Compilação
Para compilar o projeto e gerar os executáveis na pasta `bin/`, execute o seguinte comando na raiz do diretório:
```bash
make
