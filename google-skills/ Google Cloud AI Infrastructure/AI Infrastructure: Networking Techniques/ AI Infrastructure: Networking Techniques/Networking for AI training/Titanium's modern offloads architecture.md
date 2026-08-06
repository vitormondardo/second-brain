# A arquitetura de offloads moderna do Titanium

O Google adota uma abordagem otimizada para cargas de trabalho na construção de sua infraestrutura, empregando uma combinação de hardware dedicado e componentes de software para atender às demandas crescentes de suas cargas de trabalho. Na base dessa infraestrutura está o Titanium, um sistema de silício personalizado e construído especificamente para esse fim, além de múltiplas camadas de offloads de escala horizontal (scale-out), que juntos impulsionam melhorias no desempenho, na confiabilidade e na segurança das cargas de trabalho dos clientes.

## Sem offloads (abordagem tradicional)
Neste estágio inicial, a VM (host) executa tanto as cargas de trabalho do usuário (suas aplicações) quanto todos os serviços principais de infraestrutura (segurança, rede e armazenamento).
* **Desvantagem:** Os núcleos de CPU alocados para a sua carga de trabalho precisam gastar ciclos gerenciando pacotes de rede, aplicando políticas de segurança e lidando com E/S de armazenamento. Essa sobrecarga consome recursos diretamente das suas aplicações, limitando o desempenho e a taxa de transferência (throughput).

## Geração 1: Com offloads no host
Para liberar a CPU principal, um hardware especializado — a Placa de Interface de Rede Inteligente (SmartNIC) — é introduzido diretamente na máquina host. Os serviços de infraestrutura (segurança, rede e armazenamento) são movidos (ou seja, sofreram *offload*) da CPU principal para essa SmartNIC dedicada.
* **Vantagem:** Isso melhora significativamente o desempenho. As cargas de trabalho do usuário obtêm o benefício da capacidade total da CPU principal, já que a SmartNIC lida com o trabalho pesado das tarefas de infraestrutura.

## Geração 2: Offloads no host + offloads de escala horizontal (scale-out)
Esta é a arquitetura Titanium, que leva o processo de *offload* um passo além, de forma crítica. Ela combina a SmartNIC no host (para tarefas básicas e de baixa latência) com uma camada de servidores de escala horizontal fora do host (frequentemente rodando em uma plataforma como o Google Borg).
* **O diferencial principal do Titanium:** Ao contrário de abordagens concorrentes que utilizam apenas offloads e aceleradores no host, o Titanium combina de forma única aceleradores inteligentes no host com uma camada de servidores de escala horizontal fora do host.

---

## O poder dos offloads de escala horizontal (scale-out)

Esta abordagem híbrida exclusiva oferece vantagens significativas:

* **Escalabilidade massiva:** Ao distribuir tarefas de infraestrutura complexas e intensivas em recursos (como determinados processamentos de rede ou gerenciamento de armazenamento em larga escala) para clusters dedicados e escaláveis (Google Borg), podemos dimensionar o desempenho muito além do que uma única máquina host consegue suportar.
* **Preço/desempenho líderes de mercado:** Descarregar tarefas para hardware centralizado e especializado que pode ser compartilhado de forma eficiente entre muitos usuários melhora drasticamente a utilização de recursos. Essa eficiência traduz-se diretamente em uma relação preço/desempenho líder de mercado para produtos construídos sobre esta arquitetura, como o Hyperdisk.
* **Segurança e confiabilidade aprimoradas:** Centralizar e isolar funções essenciais de infraestrutura (segurança, rede e armazenamento) fornece um limite de segurança mais forte e gerenciável, além de melhorar a confiabilidade geral do sistema.

Em suma, a arquitetura de Geração 2 do Titanium é a mudança fundamental que nos permite entregar serviços de nuvem superiores, mais rápidos e mais econômicos, tratando as funções de infraestrutura como um serviço externo e escalável, em vez de uma limitação fixa do host.
<img width="1768" height="688" alt="Titanium" src="https://github.com/user-attachments/assets/a0332069-64c5-41f6-a900-4bde282e9db2" />
