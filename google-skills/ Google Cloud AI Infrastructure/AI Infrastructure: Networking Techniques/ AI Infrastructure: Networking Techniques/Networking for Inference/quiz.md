## 1. Como o GKE Inference Gateway utiliza o protocolo "ORCA" para melhorar o desempenho da inferência de IA Generativa?

* **Resposta:** **Ele relata a profundidade da fila do LLM para o balanceador de carga para um roteamento inteligente.**
* **Explicação:** O protocolo ORCA (*Open Request Cost Aggregation*) é utilizado para transmitir dados de desempenho em tempo real — como a profundidade da fila do modelo (*queue depth*) — dos servidores de modelo para o balanceador de carga. Isso permite que o sistema tome decisões de roteamento inteligentes e dinâmicas, enviando novas requisições para a instância menos sobrecarregada, o que reduz drasticamente a latência (melhorias de 5 a 10 vezes).

---

## 2. Qual é o principal benefício do "balanceamento de carga consciente de prefixo" (Prefix-aware load balancing) no GKE Inference Gateway?

* **Resposta:** **Ele melhora o Tempo até o Primeiro Token (TTFT) roteando solicitações com o mesmo prefixo para os mesmos aceleradores.**
* **Explicação:** Aplicações de IA generativa (como chatbots) frequentemente reutilizam os mesmos prefixos em chamadas de API. O balanceamento consciente de prefixo direciona solicitações com o mesmo prefixo para o acelerador que já possui o cache de chave-valor (KV cache) correspondente, melhorando a latência de TTFT em até 96% em picos de vazão e evitando pontos de superaquecimento (*hotspots*).

---

## 3. De acordo com as melhores práticas do curso, como você deve configurar a conta de serviço do Gerenciamento de Identidade e Acesso (IAM) para uma implantação no AI Hypercomputer?

* **Resposta:** **Crie uma conta de serviço dedicada e conceda apenas as funções necessárias, como `roles/compute.admin`.**
* **Explicação:** Para garantir a segurança e o sucesso das implantações em ambientes de produção, deve-se seguir o princípio do privilégio mínimo. Isso é feito criando contas de serviço dedicadas e concedendo exclusivamente as permissões estritamente necessárias, evitando o uso de contas padrão excessivamente permissivas.
