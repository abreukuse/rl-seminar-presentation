# Estrutura da Apresentação

**Título:** RL Multi-Objetivo Aplicado ao Problema Integrado de Patrulha e Despacho

**Total de Slides:** 15

---

### **Parte 1: O Problema e o Panorama das Soluções (Slides 2-7)**

**Slide 2: O Problema Central: Um Conflito de Objetivos**
* **Tópico:** Definição do problema integrado de patrulhamento e envio de viaturas.
* **Conteúdo:**
    * Operações policiais têm dois objetivos conflitantes e simultâneos.
    * **1. Patrulhamento Proativo:** Maximizar a *presença* em áreas de alto risco ("hotspots") para dissuadir o crime. Este é um problema de planejamento.
    * **2. Despacho Reativo:** Minimizar o *tempo de resposta* a novas chamadas de emergência dinâmicas. Este é um problema de alocação em tempo real.
    * **O Desafio:** Cada vez que uma unidade é despachada *reativamente*, ela compromete o plano de patrulha *proativo*. Este é um problema "bi-objetivo".

**Slide 3: Um Panorama das Soluções**
* **Tópico:** Visão geral de alto nível de como os pesquisadores tentam resolver isso.
* **Conteúdo:**
    * Este problema é complexo, misturando previsão espaço-temporal, roteamento e eventos estocásticos em tempo real.
    * Examinaremos brevemente três filosofias de solução principais:
        1.  Modelos Híbridos Heurísticos (ex: Simulação + Meta-heurísticas)
        2.  Modelos MARL Descentralizados (RL Multiagente)
        3.  Modelos MORL Centralizados (RL Multi-objetivo)

**Slide 4: Abordagem 1: A Híbrida-Heurística (ex: Simões Júnior & Borenstein, 2025)**
* **Tópico:** Breve explicação da abordagem baseada em simulação.
* **Conteúdo:**
    * **Conceito:** Modelar todo o sistema usando simulação de eventos discretos.
    * **Método (Simões Júnior & Borenstein, 2025):**
        * **Prever:** Usar Machine Learning (ex: XGBoost) para prever *hotspots dinâmicos* (risco de crime).
        * **Patrulhar:** Usar uma meta-heurística, como **Otimização por Colônia de Formigas (ACO)**, para encontrar rotas de patrulha proativas ideais com base nessas previsões.
        * **Testar:** Executar uma simulação para ver como essas rotas de patrulha são interrompidas por chamadas aleatórias e medir o desempenho.
    * **Conclusão:** Esta é uma abordagem de "otimizar e depois simular".

**Slide 5: Abordagem 2: O Modelo Multiagente (ex: Repasky et al., 2024)**
* **Tópico:** Breve explicação da abordagem MARL descentralizada.
* **Conteúdo:**
    * **Conceito:** Tratar cada viatura de patrulha como um "agente" de aprendizado independente em um sistema de RL Multiagente (MARL).
    * **Método (Repasky et al., 2024):**
        * Este é um sistema **heterogêneo** com $N+1$ agentes.
        * **$N$ Agentes Patrulheiros:** Cada unidade aprende sua *própria* política de patrulha (para onde se mover) usando uma Deep Q-Network (DQN) compartilhada.
        * **1 Agente Despachante:** Um agente central aprende a política de *despacho* (quem enviar) usando um método diferente (MIP + VFA).
    * **Conclusão:** Esta é uma abordagem "descentralizada" onde os agentes aprendem comportamentos individuais.

**Slide 6: Abordagem 3: Conceitos de RL Multi-Objetivo (MORL)**
* **Tópico:** O tópico conceitual central do seminário.
* **Conteúdo:**
    * O RL padrão maximiza uma única recompensa escalar $R$.
    * O MORL maximiza um *vetor* de recompensas: $\vec{R} = \langle R_{\text{resposta}}, R_{\text{presença}} \rangle$.
    * **O Desafio do MORL:** Como aprender uma política ótima quando "ótimo" é subjetivo? (10% de melhora na resposta vale 20% de piora na patrulha?)
    * **Solução Comum: Escalarização (Soma Ponderada).**
        * Combinar objetivos em uma recompensa: $R_{\text{total}} = w_1 \times R_{\text{resposta}} + w_2 \times R_{\text{presença}}$.
        * **Problema:** Este é o método padrão, mas é falho. Os resultados são altamente sensíveis aos pesos ($w_1, w_2$), que são arbitrários e difíceis de justificar.

**Slide 7: Transição: Uma Abordagem MORL Superior**
* **Tópico:** Preparando o terreno para o "deep dive" no artigo principal.
* **Conteúdo:**
    * As falhas do MORL de soma ponderada motivam a busca por métodos melhores.
    * As abordagens "conjuntas" (como as de Joe et al. e Repasky et al.) são as mais avançadas, pois resolvem ambos os problemas de uma vez.
    * Vamos agora analisar a fundo o artigo de **Joe, Lau, & Pan (2022)**, que introduz um modelo MORL centralizado que evita o problema da "soma ponderada".

---

### **Parte 2: Estudo de Caso - Joe, Lau, & Pan (2022) (Slides 8-12)**

**Slide 8: Visão Geral e Formulação (Joe, Lau, & Pan, 2022)**
* **Tópico:** Introduzindo o artigo principal.
* **Conteúdo:**
    * **Título:** "Reinforcement Learning Approach to Solve Dynamic Bi-objective Police Patrol Dispatching and Rescheduling Problem".
    * **Formulação:** Um **MDP Centralizado de Agente Único**.
    * **O "Agente" de RL é:** O *planejador central* ou sistema despachante.
    * **Os "Atores" são:** As unidades de patrulha, que são simplesmente recursos a serem controlados.
    * Isto *não* é MARL; as próprias unidades de patrulha não aprendem.

**Slide 9: O MDP: Estado e Ação**
* **Tópico:** Definição dos componentes do modelo.
* **Conteúdo:**
    * **Estado ($S_k$):** Um "snapshot" completo do sistema quando um incidente ($\omega_k$) ocorre.
        * Inclui a hora atual ($t_k$), todos os cronogramas conjuntos ($\delta(k)$), e todos os status de patrulha ($\sigma(k)$).
    * **Ação ($x_k$):** Uma única "tupla de decisão" complexa: $\langle x_k^i, x_k^t, \delta^x(k) \rangle$.
        * $x_k^i$: **Quem** despachar.
        * $x_k^t$: **Quando** despachá-los.
        * $\delta^x(k)$: O **novo cronograma conjunto completo** para *todas as outras* unidades de patrulha.
    * **Insight Chave:** O espaço de ação é enorme. Não é apenas "despachar unidade 3", é "despachar unidade 3 E fazer a unidade 1 cobrir sua rota, unidade 2 mudar para o sul..."

**Slide 10: A Solução: Um Híbrido de VFA + Heurística**
* **Tópico:** A arquitetura de solução em duas partes do artigo.
* **Conteúdo:**
    * Eles resolvem o problema do "espaço de ação de alta dimensionalidade" dividindo o trabalho.
    * **Treinamento Offline (O "Cérebro"):**
        * Uma Rede de Função Valor (Value Function Network - $\hat{V}$) é treinada offline usando "Dados Históricos" (Experience Replay).
        * Esta rede aprende uma coisa: o *valor futuro esperado* de qualquer estado de cronograma.
    * **Execução Online (O "Gerador de Ação"):**
        * Quando um incidente ocorre, uma "Heurística de Reagendamento" (baseada em Ejection Chains) *gera* uma lista de *potenciais e viáveis* novos cronogramas (ações).
        * A rede $\hat{V}$ pré-treinada avalia cada ação desta lista.
        * O sistema escolhe a ação $x_k^*$ que maximiza:
            $$x_k^* = \text{argmax}_{x_k} \{ \text{Recompensa Imediata} + \gamma \hat{V}(S_k^x) \}$$

**Slide 11: A Inovação Bi-Objetivo (Sem Soma Ponderada)**
* **Tópico:** A nova função de recompensa do artigo.
* **Conteúdo:**
    * Esta é a solução para o problema do MORL do Slide 6.
    * Em vez de uma soma ponderada $w_1A + w_2B$, o artigo usa uma função de recompensa *multiplicativa* e *diferencial* (Equação 4).
    * $$R(S_k, x_k) = f_r(x_k) \times f_p(\delta^x(k)) - f_p(\delta(k))$$
    * **Em Português:**
        * $f_r(x_k)$: O "Sucesso da Resposta" (um multiplicador: 1.0 para sucesso, 0.5 para falha, 0 para não resposta).
        * $f_p(\delta^x(k)) - f_p(\delta(k))$: A "Mudança na Presença da Patrulha" (o valor do novo cronograma menos o valor do antigo).
    * **Por que é inteligente:** Uma "boa" resposta a um incidente é *escalonada pelo quanto ela prejudica* o plano de patrulha proativo. Uma resposta falha que também destrói a patrulha é fortemente penalizada.

**Slide 12: Resultados Chave**
* **Tópico:** Por que essa abordagem funciona.
* **Conteúdo:**
    * **"Joint Learning" > "Two-Stage":** O modelo híbrido deles (onde o $\hat{V}$ *guia* a heurística) supera fortemente o método comum "Two-Stage" (onde o RL *apenas* escolhe o agente).
    * **Computacionalmente Viável:** O sistema toma uma decisão (Despacho + Reagendamento) "quase instantaneamente" (operacionalmente <10 segundos).

---

### **Parte 3: Conclusão (Slides 13-15)**

**Slide 13: Resumo das Abordagens**

* **Tópico:** Recapitulando os pontos principais do seminário.
* **Conteúdo:**
    * **Problema:** Patrulha Conjunta (Proativa) & Despacho (Reativo).
    * **Híbrida/Heurística (Simões Júnior & Borenstein, 2025):** Simulação poderosa, mas depende de heurísticas predefinidas (ACO).
    * **MARL (Repasky et al., 2024):** Verdadeiramente "baseado em agentes", mas decompõe o problema.
    * **MORL Centralizado (Joe, Lau, & Pan, 2022):** Resolve o problema holisticamente usando um único "cérebro" (VFA) para selecionar inteligentemente de uma lista de "ações" complexas geradas por heurística.

**Slide 14: Principais Conclusões**

* **Tópico:** Conclusões finais.
* **Conteúdo:**
    * O problema de "Patrulha Conjunta & Despacho" é um campo de testes perfeito e do mundo real para RL avançado.
    * A arquitetura de Joe, Lau, & Pan (2022) (Gerador-Heurístico + Avaliador-VFA) é um padrão muito eficaz para resolver problemas de RL com espaços de ação massivos e complexos.
    * A nova função de recompensa multiplicativa é uma maneira inteligente de resolver um problema bi-objetivo sem a ambiguidade das somas ponderadas.

**Slide 15: Obrigado / Perguntas**