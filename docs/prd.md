# 📄 Product Requirements Document (PRD)

**Projeto:** MuletAí
**Versão:** 1.0.0
**Última atualização:** 2026-09-21

> 🤖 **Este documento é a fonte da verdade sobre O QUE o produto faz.** Regra de
> negócio que não estiver aqui não existe — nem para a equipe, nem para a IA.
> Tecnologia **não** se discute aqui: isso é assunto do `architecture.md`.
>
> ✍️ **Não preencha na mão:** rode `/utf-prd` — a entrevista percorre as seções
> abaixo, na ordem, e confere o resultado contra a ficha da disciplina
> (`docs/checklist.md`). As respostas são suas; o agente só organiza.

---

## 🎯 1. Visão Geral e Objetivo

**O problema:** quem fica enfermo ou em recuperação depende de equipamentos médicos temporários (muletas, andadores, cadeiras de rodas, camas hospitalares), e hoje só os consegue por networking e boca a boca. Quem tem um equipamento parado em casa, por sua vez, não sabe quem precisa dele.

**A solução:** uma rede de apoio filantrópica que conecta quem precisa de equipamentos a quem pode emprestá-los. Cada pessoa cadastra os equipamentos que tem e os empresta diretamente. Quem precisa faz um pedido e entra numa fila; o sistema liga o primeiro da fila ao equipamento disponível que está na vez. Existe ainda um centro de distribuição físico, com estoque próprio, abastecido por equipamentos comprados com as contribuições financeiras de outros usuários. Essas contribuições alimentam um caixa único, que serve o pedido em primeiro lugar quando não há equipamento disponível para ele.

**Como saberemos que deu certo:** uma pessoa se cadastra e cadastra seus equipamentos. Quem precisa faz um pedido e entra na fila. Quando não há equipamento disponível, ele é comprado com o dinheiro das contribuições para o primeiro pedido. A posse do equipamento passa ao solicitante.

---

## 📖 2. Glossário Ubíquo

> Os termos do negócio, como o cliente fala. É daqui que o `architecture.md`
> deriva os nomes das entidades.

| Termo | Significa | Não confundir com |
| :---- | :-------- | :---------------- |
| **Doador** | Quem cadastra e empresta ou doa equipamentos que tem | Apoiador |
| **Solicitante** | Quem faz um pedido e entra na fila | Doador |
| **Apoiador** | Quem contribui com dinheiro para o caixa | Doador (dá equipamento, não dinheiro) |
| **Equipamento** | O que uma pessoa cadastra ou pede (muleta, andador, cadeira de rodas, cama hospitalar), escolhido de uma lista mantida pela moderação | Tipo |
| **Tipo** | Variação de um equipamento (canadense, para muleta). Opcional | Equipamento |
| **Campo customizável** | Informação extra que a moderação define para um equipamento, obrigatória ou opcional (muleta: Quantidade, Uma ou Par) | Quantidade geral, que não existe |
| **Pedido** | O que o solicitante precisa. É a entrada dele na fila | Fila |
| **Fila** | A ordem dos pedidos por o que se precisa. Vale o mais antigo | Pedido |
| **Vez** | Quando um equipamento disponível é reservado para um pedido. A pessoa tem 1 semana para obtê-lo | Posse |
| **Disponível** | Equipamento que ninguém está usando | Reservado |
| **Posse** | O registro de quem está com o equipamento. Passa quando o solicitante confirma o recebimento | Propriedade (emprestar vale como doar) |
| **Estoque pessoal** | Os equipamentos que uma pessoa cadastrou e tem em casa | Centro de distribuição |
| **Centro de distribuição** | Lugar físico com o estoque dos equipamentos comprados com o dinheiro do site | Estoque pessoal |
| **Caixa** | O dinheiro das contribuições. É único, cresce independente do pedido e compra o equipamento do primeiro pedido | Fundo de um pedido |
| **Contribuição** | Dinheiro (mínimo R$ 5,00) que o apoiador paga para o caixa | Doação de equipamento |
| **Extraviado** | Estado de um equipamento perdido. Não é escolhido no cadastro | Danificado |
| **Motivo de recusa** | A razão que o moderador registra ao recusar um pedido | Motivo de banimento |
| **Painel** | A tela que mostra o pedido em primeiro lugar e quanto falta para comprá-lo | Fila |
| **Lembrete** | A pergunta gentil ao possuidor: "Você ainda está utilizando?" | Cobrança de devolução |
| **Sub-fila** | *(`Could`)* Onde quem perdeu a vez espera confirmar | Fim da fila |

---

## 👤 3. Atores e Permissões

> ⚠️ A coluna **"Não pode"** vira Guard e controle de role na API.

| Ator | Quem é | Pode | Não pode |
| :--- | :----- | :--- | :------- |
| **Visitante** | Quem não tem conta | Cadastrar-se e entrar | Ver qualquer coisa do sistema |
| **Usuário** | Qualquer pessoa cadastrada | Cadastrar equipamentos, fazer pedidos, contribuir, conversar, denunciar, excluir a conta | Gerir o centro, moderar, nomear papéis |
| **Gestor do centro** | Usuário com permissão de gestão. Por enquanto, o admin faz este papel | Registrar entrada e saída no centro; tudo o que o usuário faz | Moderar denúncias, nomear papéis |
| **Moderador** | Usuário com permissão de moderação | Aprovar ou recusar equipamentos e tipos propostos, recusar pedidos com motivo, analisar denúncias, banir e reintegrar, aprovar a troca de e-mail de login, marcar extravio; tudo o que o usuário faz | Moderar caso em que é parte (exceto recusar o próprio pedido), moderar outro moderador, gerir o estoque do centro |
| **Admin** | Usuário com permissão máxima | Moderar moderadores, nomear e remover gestores e moderadores, definir o preço, registrar a compra, receber as contribuições; tudo o que os outros fazem | Nada |
| **Empresa de saúde** | *(`Could`)* Fornecedora no pregão | Dar lances | *(a definir se a US28 for promovida)* |
| **Recebedor** | *(`Could`)* Quem compra com o dinheiro | Registrar a compra com a nota | *(a definir se a US29 for promovida)* |

---

## 📝 4. Escopo Funcional (User Stories)

> Uma story por vez, no formato do modelo abaixo. Cada uma carrega dois eixos:
> **Prioridade (MoSCoW)** — `Must Have` é o escopo comprometido do projeto
> (o escopo mínimo da ficha é `Must Have` por definição); `Should`/`Could`
> entram se sobrar tempo, mas ficam documentadas — nada se perde; o
> `Won't Have` vira item da seção *Fora de Escopo* — e **Tamanho (esforço)** —
> `S` cabe numa sessão, `M` vira algumas tarefas no plano, `L` pede divisão.
> Toda story nasce `Draft` — **só você promove a `Ready`**; `Live` é quando o PR
> da história mescla (o auditor final confere).

<!-- Status: `⚪ Draft` (não codificar) · `🟡 Ready` (vira Issue) · `🟢 Live` (PR mesclado) -->

### US01 — Gerenciar minha conta · `Must Have` · `M` · Status: `⚪ Draft`

**Como** pessoa que quer participar da rede, **eu quero** me cadastrar, entrar no sistema e alterar meus dados **para que** eu possa usar a plataforma com a minha identidade.

**Critérios de aceite:**

- [ ] **1.** **Dado** que não tenho conta, **quando** informo meu nome e um e-mail de login válido (e a senha), **então** a conta é criada com os demais dados em branco, e posso entrar.
- [ ] **2.** **Dado** que já existe uma conta com aquele e-mail de login, **quando** tento me cadastrar com ele, **então** o cadastro é recusado e o sistema diz que o e-mail já está em uso.
- [ ] **3.** **Dado** que deixo o nome ou o e-mail de login em branco, **quando** tento me cadastrar, **então** o cadastro é recusado e o sistema indica o campo que falta.
- [ ] **4.** **Dado** que tenho conta, **quando** informo meu e-mail de login e a senha corretos, **então** entro no sistema.
- [ ] **5.** **Dado** que informo e-mail ou senha errados, **quando** tento entrar, **então** o acesso é recusado, sem revelar qual dos dois está errado.
- [ ] **6.** **Dado** que estou logado, **quando** altero nome, e-mail de contato, telefones, WhatsApp ou endereço, **então** os novos dados são salvos e aparecem quando eu consulto meu perfil.
- [ ] **7.** **Dado** que estou logado, **quando** tento alterar meu e-mail de login diretamente, **então** a alteração é recusada e o sistema informa que a troca exige um pedido analisado por um moderador.
- [ ] **8.** **Dado** que estou logado, **quando** apago meu nome, **então** a alteração é recusada, porque o nome é obrigatório.
- [ ] **9.** **Dado** que não estou logado, **quando** tento ver ou alterar os dados de uma conta, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN01, RN02, RN18, RN26

---

### US02 — Cadastrar um equipamento médico · `Must Have` · `M` · Status: `⚪ Draft`

**Como** doador, **eu quero** cadastrar um equipamento médico que tenho **para que** ele fique disponível na rede e alguém que precise possa pedi-lo.

**Depende de:** US01.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** escolho na lista qual é o equipamento (exemplo: muleta), informo o estado (Danificado, Utilizável, Perfeito estado ou Novo), envio de 1 a 5 fotos e escolho um endereço, **então** o equipamento é cadastrado e posso abri-lo para ver o que informei.
- [ ] **2.** **Dado** que o equipamento tem tipos (exemplo: canadense), **quando** não escolho nenhum, **então** o cadastro é aceito, porque o tipo é opcional.
- [ ] **3.** **Dado** que envio nenhuma foto, **quando** tento cadastrar, **então** o cadastro é recusado e o sistema diz que a foto é obrigatória.
- [ ] **4.** **Dado** que envio mais de 5 fotos, **quando** tento cadastrar, **então** o cadastro é recusado e o sistema informa o limite de 5.
- [ ] **5.** **Dado** que não escolho o equipamento ou o estado, **quando** tento cadastrar, **então** o cadastro é recusado e o sistema indica o campo que falta.
- [ ] **6.** **Dado** que o equipamento tem um campo customizável obrigatório (exemplo: Quantidade, Uma ou Par, nas muletas), **quando** o deixo em branco, **então** o cadastro é recusado e o sistema indica qual campo.
- [ ] **7.** **Dado** que informei o tipo e os campos customizáveis, **quando** alguém abre o equipamento, **então** eles aparecem de forma clara.
- [ ] **8.** **Dado** que o estado é Danificado, **quando** deixo a descrição em branco, **então** o cadastro é recusado e o sistema diz que a descrição é obrigatória.
- [ ] **9.** **Dado** que o estado não é Danificado, **quando** deixo a descrição em branco, **então** o cadastro é aceito.
- [ ] **10.** **Dado** que o equipamento está Danificado, **quando** alguém o abre, **então** vê o status e a descrição do dano.
- [ ] **11.** **Dado** que tenho endereço principal na conta, **quando** escolho usá-lo, **então** o equipamento fica com esse endereço.
- [ ] **12.** **Dado** que quero outro endereço, **quando** o adiciono na hora, **então** o equipamento usa esse endereço e ele fica salvo para os próximos.
- [ ] **13.** **Dado** que minha conta não tem endereço, **quando** tento cadastrar sem adicionar nenhum, **então** o cadastro é recusado e o sistema diz que o endereço é obrigatório.
- [ ] **14.** **Dado** que não estou logado, **quando** tento cadastrar um equipamento ou abrir um, **então** o sistema me pede para entrar.
- [ ] **15.** **Dado** que estou logado, **quando** marco se posso entregar dentro da minha cidade e informo os horários em que estou em casa, **então** essas informações ficam registradas no equipamento, e a marca de entrega aparece para quem o abre.
- [ ] **16.** **Dado** que deixo a entrega e os horários em branco, **quando** cadastro, **então** o cadastro é aceito, porque são opcionais.
- [ ] **17.** **Dado** que informei horários em que estou em casa, **quando** outra pessoa abre o equipamento, **então** os horários não aparecem para ela.
- [ ] **18.** **Dado** que o equipamento tem endereço, **quando** outra pessoa sem a vez o abre, **então** vê só a cidade, e não o endereço completo.

**Regras relacionadas:** RN03, RN04, RN05, RN06, RN07, RN08, RN18, RN19

---

### US03 — Fazer um pedido · `Must Have` · `M` · Status: `⚪ Draft`

**Como** solicitante, **eu quero** fazer um pedido de equipamento médico **para que** eu entre na fila e receba um equipamento assim que houver um.

**Depende de:** US01.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** escolho na lista qual é o equipamento que preciso (exemplo: muleta), informo os campos customizáveis que ele exige e escolho um endereço, **então** o pedido é criado, entro na fila com a minha posição visível e posso abrir o pedido para ver o que informei.
- [ ] **2.** **Dado** que o equipamento tem tipos, **quando** não escolho nenhum, **então** o pedido é aceito, porque o tipo é opcional. **E quando** escolho um, **então** só equipamentos desse tipo o atendem.
- [ ] **3.** **Dado** que faço o pedido e não há equipamento disponível que atenda, **quando** ele é criado, **então** fica **Pendente** na fila.
- [ ] **4.** **Dado** que o formulário do pedido está aberto, **quando** o preencho, **então** não posso escolher o estado do equipamento.
- [ ] **5.** **Dado** que não escolho o equipamento, **quando** tento fazer o pedido, **então** ele é recusado e o sistema indica o campo que falta.
- [ ] **6.** **Dado** que o equipamento tem um campo customizável obrigatório (exemplo: Quantidade nas muletas), **quando** o deixo em branco, **então** o pedido é recusado e o sistema indica qual campo.
- [ ] **7.** **Dado** que deixo a descrição em branco, **quando** faço o pedido, **então** ele é aceito, porque a descrição é opcional.
- [ ] **8.** **Dado** que já tenho um pedido aberto, **quando** faço outro, **então** ele é criado, porque não há limite de pedidos abertos.
- [ ] **9.** **Dado** que escolho o endereço principal ou adiciono outro na hora, **quando** faço o pedido, **então** ele usa esse endereço, e o novo fica salvo para os próximos.
- [ ] **10.** **Dado** que minha conta não tem endereço, **quando** tento fazer o pedido sem adicionar nenhum, **então** ele é recusado e o sistema diz que o endereço é obrigatório.
- [ ] **11.** **Dado** que informo se posso buscar o equipamento e os horários em que estou em casa, **quando** faço o pedido, **então** essas informações ficam registradas no pedido. **E quando** deixo os dois em branco, **então** o pedido é aceito, porque são opcionais.
- [ ] **12.** **Dado** que o pedido tem endereço e horários, **quando** outra pessoa que não é a outra parte o abre, **então** vê só a cidade, e não vê os horários.
- [ ] **13.** **Dado** que tenho um pedido Pendente, **quando** o cancelo, **então** ele vira **Cancelado**, saio da fila e quem estava atrás sobe. Se havia equipamento reservado para mim, ele volta a ficar disponível.
- [ ] **14.** **Dado** que não estou logado, **quando** tento fazer um pedido, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN03, RN09, RN10, RN11, RN18, RN19

---

### US04 — Contribuir com dinheiro · `Must Have` · `M` · Status: `⚪ Draft`

**Como** apoiador, **eu quero** contribuir com dinheiro **para que** a rede possa comprar equipamentos para quem precisa.

**Depende de:** US01.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** informo R$ 5,00 ou mais e concluo o pagamento, **então**, quando a confirmação chega, o valor é somado ao caixa.
- [ ] **2.** **Dado** que o pagamento ainda não foi confirmado, **quando** olho o caixa, **então** o valor ainda não aparece nele.
- [ ] **3.** **Dado** que o pagamento é recusado, **quando** tento contribuir, **então** nada é somado e o sistema me informa.
- [ ] **4.** **Dado** que abandono o pagamento sem concluir, **quando** volto ao sistema, **então** nada é somado ao caixa.
- [ ] **5.** **Dado** que informo menos de R$ 5,00, **quando** tento contribuir, **então** a contribuição é recusada e o sistema informa o mínimo.
- [ ] **6.** **Dado** que uma contribuição confirmada faz o caixa cobrir o preço do pedido em primeiro lugar, **quando** a confirmação chega, **então** o pedido passa a **Em processo de compra** e o admin é avisado.
- [ ] **7.** **Dado** que não estou logado, **quando** tento contribuir, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN15, RN16, RN18

---

### US05 — Definir o preço e registrar a compra · `Must Have` · `S` · Status: `⚪ Draft`

**Como** admin, **eu quero** definir o preço do equipamento do primeiro pedido e registrar a compra **para que** o caixa reflita o dinheiro gasto e o pedido siga adiante.

**Depende de:** US03.

**Critérios de aceite:**

- [ ] **1.** **Dado** que sou admin, **quando** abro o painel do admin, **então** vejo o saldo do caixa e o pedido Pendente em primeiro lugar.
- [ ] **2.** **Dado** que há um pedido em primeiro lugar, **quando** informo o preço do equipamento, **então** o preço passa a valer.
- [ ] **3.** **Dado** que o preço mudou (exemplo: de 60 para 70), **quando** o altero, **então** o novo valor vale dali em diante e o caixa não muda.
- [ ] **4.** **Dado** que o saldo cobre a compra, **quando** registro o valor pago, **então** ele sai do caixa e o que sobrar continua nele (exemplo: caixa de 100, compra de 70, ficam 30).
- [ ] **5.** **Dado** que o pedido está Em processo de compra e o valor real é maior que o saldo, **quando** o devolvo, **então** ele volta a **Pendente** e o caixa não muda.
- [ ] **6.** **Dado** que registro a compra, **quando** a gravo, **então** o pedido passa a **Finalizado** e o próximo Pendente passa a ser o primeiro.
- [ ] **7.** **Dado** que informo preço zero ou negativo, **quando** tento defini-lo, **então** o sistema recusa.
- [ ] **8.** **Dado** que não há pedido em primeiro lugar, **quando** tento definir o preço ou registrar a compra, **então** o sistema informa que não há pedido.
- [ ] **9.** **Dado** que não sou admin, **quando** tento definir o preço ou registrar a compra, **então** a ação é recusada.

**Regras relacionadas:** RN10, RN15, RN16, RN17, RN23

---

### US06 — Ver quem precisa de ajuda · `Must Have` · `M` · Status: `⚪ Draft`

**Como** apoiador, **eu quero** ver no painel o pedido em primeiro lugar que ainda não foi contemplado **para que** eu saiba a quem estou ajudando e contribua.

**Depende de:** US03 e US05. O painel leva à US04.

**Critérios de aceite:**

- [ ] **1.** **Dado** que há um pedido Pendente em primeiro lugar, **quando** abro o painel, **então** vejo o equipamento e a mensagem *"Faltam X reais para comprar este [equipamento]. Contribua com qualquer quantia para ajudar [solicitante] a ter uma condição melhor de vida"*, com X igual ao preço menos o saldo do caixa.
- [ ] **2.** **Dado** que o solicitante escolheu ser anônimo, **quando** abro o painel, **então** a mensagem não cita o nome e diz algo como *"Sua contribuição pode ajudar o próximo da fila"*.
- [ ] **3.** **Dado** que o admin ainda não definiu o preço, **quando** abro o painel, **então** vejo o pedido e o aviso de que o preço ainda não foi definido.
- [ ] **4.** **Dado** que o admin alterou o preço, **quando** abro o painel, **então** o valor que falta aparece atualizado.
- [ ] **5.** **Dado** que o pedido entrou Em processo de compra, **quando** abro o painel, **então** ele não aparece mais, e o painel mostra o próximo Pendente, se houver.
- [ ] **6.** **Dado** que o processo falhou e o pedido voltou a Pendente, **quando** abro o painel, **então** a mensagem dele volta a aparecer.
- [ ] **7.** **Dado** que não há pedido Pendente, **quando** abro o painel, **então** vejo a mensagem de contribuir, o último pedido realizado e um carrossel dos realizados, cada um com as fotos e o agradecimento que a pessoa tiver postado.
- [ ] **8.** **Dado** que um pedido foi Recusado, **quando** abro o painel, **então** ele não aparece.
- [ ] **9.** **Dado** que estou vendo o pedido, **quando** clico em contribuir, **então** sou levado à contribuição.
- [ ] **10.** **Dado** que não estou logado, **quando** tento abrir o painel, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN10, RN15, RN16, RN20

---

### US07 — Receber o equipamento que está na vez · `Must Have` · `M` · Status: `⚪ Draft`

**Como** solicitante, **eu quero** que o sistema me ligue ao equipamento disponível que está na vez **para que** eu receba o que preciso sem escolher a quem pedir.

**Depende de:** US02 e US03.

**Critérios de aceite:**

- [ ] **1.** **Dado** que tenho um pedido na fila e existe equipamento disponível que atende a ele, **quando** sou o primeiro que esse equipamento atende, **então** o equipamento fica reservado para mim e passo a ter a vez.
- [ ] **2.** **Dado** que vários equipamentos atendem, **quando** o cruzamento acontece, **então** recebo o que foi cadastrado primeiro.
- [ ] **3.** **Dado** que o equipamento na vez já foi emprestado ou reservado para outra pessoa, **quando** o sistema procura, **então** passa para o próximo disponível mais antigo.
- [ ] **4.** **Dado** que o único equipamento que atende está Danificado, **quando** o sistema procura, **então** ele não é reservado.
- [ ] **5.** **Dado** que a reserva acontece, **quando** ela é feita, **então** sou avisado.
- [ ] **6.** **Dado** que tenho a vez, **quando** abro o equipamento, **então** vejo os horários em que o dono está em casa, se ele pode entregar na cidade e o endereço completo.
- [ ] **7.** **Dado** que tenho a vez e passa 1 semana sem eu obter o equipamento, **quando** o prazo vence, **então** vou para o fim da fila e o equipamento volta a ficar disponível.
- [ ] **8.** **Dado** que um equipamento novo que atende a um pedido meu é cadastrado, **quando** ele fica disponível, **então** ele é reservado para o pedido mais antigo que ele atende.
- [ ] **9.** **Dado** que o meu equipamento foi reservado para um pedido, **quando** o abro, **então** vejo o endereço do solicitante, se ele pode buscar e os horários em que ele está em casa.

**Regras relacionadas:** RN08, RN09, RN11, RN12, RN14, RN19

---

### US08 — Buscar equipamentos com filtros · `Must Have` · `S` · Status: `⚪ Draft`

**Como** solicitante, **eu quero** buscar equipamentos por equipamento, tipo e características **para que** eu veja o que está sendo emprestado e entre na fila.

**Depende de:** US02 e US03.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** busco escolhendo o equipamento e, se quiser, o tipo e os campos customizáveis, **então** vejo os equipamentos disponíveis que atendem.
- [ ] **2.** **Dado** que há equipamentos Danificados entre os resultados, **quando** vejo a lista, **então** eles aparecem com o status e a descrição do dano.
- [ ] **3.** **Dado** que vejo um equipamento que quero, **quando** clico em "quero", **então** abre o pedido já preenchido com aquele equipamento e tipo, e eu só completo o endereço.
- [ ] **4.** **Dado** que a busca não encontra nada, **quando** vejo o resultado, **então** o sistema informa e me deixa fazer um pedido.
- [ ] **5.** **Dado** que não estou logado, **quando** tento buscar, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN04, RN08, RN09, RN18

---

### US09 — Passar a posse do equipamento · `Must Have` · `M` · Status: `⚪ Draft`

**Como** solicitante, **eu quero** confirmar que recebi o equipamento **para que** o sistema registre que estou com ele.

**Depende de:** US07.

**Critérios de aceite:**

- [ ] **1.** **Dado** que tenho um equipamento reservado para mim, **quando** confirmo que o recebi, **então** a posse passa a ser minha e o equipamento deixa de estar disponível.
- [ ] **2.** **Dado** que a posse passa para mim, **quando** confirmo, **então** o meu pedido passa a **Finalizado** e sai da fila.
- [ ] **3.** **Dado** que a posse passou, **quando** ela é registrada, **então** a mudança entra no histórico do equipamento com quem ficou com ele, a data e o estado, sem motivos.
- [ ] **4.** **Dado** que o equipamento é uma muleta, **quando** vou confirmar o recebimento, **então** vejo de forma clara se é Uma ou Par.
- [ ] **5.** **Dado** que o equipamento não está reservado para mim, **quando** tento confirmar o recebimento, **então** o sistema recusa.
- [ ] **6.** **Dado** que a minha reserva venceu (1 semana), **quando** tento confirmar, **então** o sistema recusa e informa que perdi a vez.
- [ ] **7.** **Dado** que não estou logado, **quando** tento confirmar o recebimento, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN05, RN10, RN12, RN14

---

### US10 — Banir uma conta · `Must Have` · `M` · Status: `⚪ Draft`

**Como** moderador, **eu quero** banir uma conta **para que** a rede fique protegida de quem a usa de forma indevida.

**Depende de:** US01.

**Critérios de aceite:**

- [ ] **1.** **Dado** que sou moderador, **quando** bano uma conta, **então** a pessoa não consegue mais entrar nem fazer nada no sistema.
- [ ] **2.** **Dado** que a conta banida tem pedidos na fila, **quando** o banimento é aplicado, **então** os pedidos passam a **Cancelado** e quem estava atrás sobe.
- [ ] **3.** **Dado** que a conta banida está com equipamentos (os dela ou emprestados), **quando** o banimento é aplicado, **então** eles passam a **Extraviado** e saem dos disponíveis.
- [ ] **4.** **Dado** que a conta banida cadastrou equipamentos que estão com outras pessoas, **quando** o banimento é aplicado, **então** eles continuam com quem está com eles.
- [ ] **5.** **Dado** que um equipamento reservado para um solicitante é da conta banida, **quando** o banimento é aplicado, **então** a reserva cai e o pedido do solicitante volta a ser cruzado.
- [ ] **6.** **Dado** que sou moderador, **quando** tento banir um moderador ou um admin, **então** o sistema recusa. Só um admin bane um moderador.
- [ ] **7.** **Dado** que sou moderador, **quando** tento banir a minha própria conta, **então** o sistema recusa.
- [ ] **8.** **Dado** que não sou moderador nem admin, **quando** tento banir uma conta, **então** a ação é recusada.
- [ ] **9.** **Dado** que sou moderador, **quando** bano uma conta sem informar o motivo, **então** o sistema recusa.
- [ ] **10.** **Dado** que informo o motivo, **quando** bano, **então** ele fica registrado.

**Regras relacionadas:** RN07, RN21, RN23

---

### US11 — Registrar entrada e saída no centro · `Should Have` · `S` · Status: `⚪ Draft`

**Como** gestor do centro, **eu quero** registrar a entrada e a saída de equipamentos **para que** o estoque do centro reflita o que há de fato.

**Depende de:** US02.

**Critérios de aceite:**

- [ ] **1.** **Dado** que sou gestor do centro, **quando** registro a entrada de um equipamento (estado, fotos e o que ele é), **então** ele passa a fazer parte do estoque do centro e fica disponível para o cruzamento.
- [ ] **2.** **Dado** que um equipamento do centro foi entregue a um solicitante, **quando** registro a saída, **então** ela fica registrada no histórico. A posse continua passando pela confirmação do solicitante (US09).
- [ ] **3.** **Dado** que não sou gestor do centro, **quando** tento registrar entrada ou saída, **então** a ação é recusada.

**Regras relacionadas:** RN08, RN14

---

### US12 — Propor e aprovar um novo equipamento ou tipo · `Should Have` · `M` · Status: `⚪ Draft`

**Como** usuário, **eu quero** propor um equipamento ou tipo que não está na lista **para que** ele possa ser cadastrado ou pedido depois de aprovado.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** proponho um equipamento ou tipo que não está na lista, **então** a proposta fica aguardando a aprovação de um moderador.
- [ ] **2.** **Dado** que sou moderador ou admin, **quando** aprovo a proposta, **então** o equipamento entra na lista, e eu defino os campos customizáveis dele, obrigatórios ou opcionais.
- [ ] **3.** **Dado** que sou moderador ou admin, **quando** recuso a proposta (exemplo: dentadura, grelha de churrasco), **então** ela não entra na lista.
- [ ] **4.** **Dado** que não sou moderador nem admin, **quando** tento aprovar ou recusar, **então** a ação é recusada.

**Regras relacionadas:** RN03, RN04, RN23

---

### US13 — Consultar o histórico do equipamento · `Should Have` · `S` · Status: `⚪ Draft`

**Como** usuário logado, **eu quero** consultar o histórico de um equipamento **para que** eu saiba por onde ele passou.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** abro o histórico, **então** vejo os eventos em ordem (adicionado, mudanças de posse e de estado), com a data, a descrição e o estado.
- [ ] **2.** **Dado** que houve mudança de posse, **quando** abro o histórico, **então** vejo quem ficou com o equipamento (o primeiro nome, com link para o perfil, ou "usuário anônimo" se a pessoa o tiver escolhido), as datas e os estados, **sem motivos**.
- [ ] **3.** **Dado** que sou moderador ou admin, **quando** abro o histórico, **então** vejo sempre a identidade real.
- [ ] **4.** **Dado** que não estou logado, **quando** tento abrir o histórico, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN14, RN18, RN20, RN23

---

### US14 — Recusar um pedido, escolhendo o motivo · `Should Have` · `M` · Status: `⚪ Draft`

**Como** moderador, **eu quero** recusar um pedido escolhendo o motivo **para que** eu remova da rede o que não é adequado (exemplo: um pedido que não é de equipamento médico).

**Critérios de aceite:**

- [ ] **1.** **Dado** que sou moderador ou admin, **quando** cadastro um motivo de recusa, **então** ele fica disponível para escolha, e só moderadores e admins veem a lista.
- [ ] **2.** **Dado** que um pedido está Pendente, **quando** o recuso escolhendo um motivo, **então** ele passa a **Recusado** e sai da fila e do painel.
- [ ] **3.** **Dado** que tento recusar sem escolher um motivo, **quando** confirmo, **então** o sistema recusa a operação.
- [ ] **4.** **Dado** que um pedido já tem motivo de recusa, **quando** aplico outro, **então** permanece o primeiro.
- [ ] **5.** **Dado** que recuso vários pedidos, **quando** escolho o mesmo motivo, **então** ele é aceito para todos.
- [ ] **6.** **Dado** que o meu pedido foi Recusado, **quando** o abro, **então** vejo o motivo da recusa.
- [ ] **7.** **Dado** que não sou moderador nem admin, **quando** tento recusar um pedido ou ver os motivos, **então** a ação é recusada.

**Regras relacionadas:** RN10, RN13, RN23

---

### US15 — Denunciar uma foto e o moderador decidir · `Should Have` · `M` · Status: `⚪ Draft`

**Como** usuário, **eu quero** denunciar uma foto imprópria **para que** um moderador a analise.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** denuncio uma foto, **então** a denúncia fica registrada e aguarda análise.
- [ ] **2.** **Dado** que a foto foi denunciada, **quando** ainda não há decisão, **então** ela continua visível.
- [ ] **3.** **Dado** que sou moderador ou admin, **quando** analiso a denúncia, **então** posso **permitir** que a foto continue aparecendo, **mantê-la oculta** ou **banir a conta** (a ação da US10).
- [ ] **4.** **Dado** que a foto foi ocultada, **quando** alguém abre o equipamento, **então** ela não aparece.
- [ ] **5.** **Dado** que já denunciei aquela foto, **quando** tento denunciá-la de novo, **então** o sistema recusa.
- [ ] **6.** **Dado** que não sou moderador nem admin, **quando** tento analisar uma denúncia, **então** a ação é recusada.
- [ ] **7.** **Dado** que não estou logado, **quando** tento denunciar, **então** o sistema me pede para entrar.
- [ ] **8.** **Dado** que a foto ocultada era a única do equipamento, **quando** ela é ocultada, **então** o equipamento sai da lista até o dono enviar outra foto.

**Regras relacionadas:** RN06, RN23

---

### US16 — Denunciar equipamento, pessoa ou mensagem · `Should Have` · `M` · Status: `⚪ Draft`

**Como** usuário, **eu quero** denunciar um equipamento, uma pessoa ou uma mensagem do chat **para que** um moderador a analise.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** denuncio um equipamento, uma pessoa ou uma mensagem, informando o motivo, **então** a denúncia fica registrada e aguarda análise.
- [ ] **2.** **Dado** que denuncio mensagens do chat, **quando** marco as que quero denunciar, **então** a denúncia leva essas mensagens e, escondida do denunciante, a marca que prova que não foram forjadas.
- [ ] **3.** **Dado** que sou moderador ou admin, **quando** analiso a denúncia, **então** posso arquivá-la ou agir (ocultar o equipamento, ou banir a conta pela US10).
- [ ] **4.** **Dado** que não sou moderador nem admin, **quando** tento analisar uma denúncia, **então** a ação é recusada.

**Regras relacionadas:** RN23, RN24

---

### US17 — Excluir minha conta · `Should Have` · `M` · Status: `⚪ Draft`

**Como** usuário, **eu quero** excluir a minha conta **para que** meus dados saiam da rede, como a LGPD prevê.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** peço para excluir a conta e confirmo, **então** ela é excluída e não consigo mais entrar.
- [ ] **2.** **Dado** que a conta foi excluída, **quando** o tempo passa, **então** os dados ficam guardados por **6 meses** sem aparecer para os outros, e depois são removidos.
- [ ] **3.** **Dado** que a conta tinha pedidos na fila, **quando** é excluída, **então** os pedidos passam a **Cancelado** e quem estava atrás sobe.
- [ ] **4.** **Dado** que a conta está com equipamentos, **quando** é excluída, **então** eles passam a **Extraviado**.
- [ ] **5.** **Dado** que não estou logado, **quando** tento excluir uma conta, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN07, RN10, RN22, RN25

---

### US18 — Conversar com a outra parte · `Must Have` · `L` · Status: `⚪ Draft`

**Como** solicitante ou doador, **eu quero** conversar com a outra parte **para que** a gente combine a entrega.

**Depende de:** US07. O `L` pede divisão antes de implementar.

**Critérios de aceite:**

- [ ] **1.** **Dado** que tenho a vez de um equipamento, **quando** abro a conversa com o dono, **então** posso trocar mensagens de texto com ele.
- [ ] **2.** **Dado** que existe uma conversa, **quando** alguém que não é uma das duas partes tenta lê-la, **então** não consegue, nem moderador.
- [ ] **3.** **Dado** que não estou logado, **quando** tento abrir uma conversa, **então** o sistema me pede para entrar.
- [ ] **4.** **Dado** que a reserva acontece, **quando** a conversa é aberta, **então** ela é entre o solicitante, o doador e aquele equipamento.
- [ ] **5.** **Dado** que o equipamento muda, **quando** há nova reserva, **então** abre-se outra conversa, separada da anterior.
- [ ] **6.** **Dado** que envio uma mensagem, **quando** ela é gravada, **então** fica com uma marca que permite provar depois que não foi alterada.

**Regras relacionadas:** RN20, RN24

---

### US19 — Lembrete gentil · `Could Have` · `S` · Status: `⚪ Draft`

**Como** pessoa que está com um equipamento, **eu quero** receber um lembrete gentil **para que** o equipamento volte à rede quando alguém precisar.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou com um equipamento e há pedido na fila que ele atenderia, **quando** passa o período (definido pelo sistema), **então** recebo: *"Precisamos de [equipamento]. Você ainda está utilizando?"*
- [ ] **2.** **Dado** que respondo que **ainda uso**, **quando** respondo, **então** nada muda até o próximo período.
- [ ] **3.** **Dado** que respondo que **não uso mais**, **quando** respondo, **então** abre a tela de cadastro de equipamento já preenchida com o que o sistema sabe, e eu completo o que falta, incluindo os horários em que estou em casa, que aqui são obrigatórios, para o equipamento voltar à rede.
- [ ] **4.** **Dado** que não há pedido na fila que o equipamento atenda, **quando** passa o período, **então** não recebo lembrete.
- [ ] **5.** **Dado** que deixo os horários em branco nessa tela, **quando** tento concluir, **então** o sistema recusa e diz que são obrigatórios.

**Regras relacionadas:** RN08, RN14

---

### US20 — Pedir a troca do e-mail de login · `Could Have` · `S` · Status: `⚪ Draft`

**Como** usuário, **eu quero** pedir a troca do meu e-mail de login **para que** eu continue entrando no sistema com o e-mail que uso.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** peço a troca informando o e-mail novo, **então** o pedido fica aguardando a análise de um moderador.
- [ ] **2.** **Dado** que sou moderador, **quando** aprovo o pedido, **então** o sistema envia uma confirmação ao e-mail novo, e só depois de confirmada ele passa a ser o e-mail de login.
- [ ] **3.** **Dado** que sou moderador, **quando** recuso o pedido, **então** o e-mail de login não muda.
- [ ] **4.** **Dado** que o e-mail novo já está em uso, **quando** peço a troca, **então** o sistema recusa.

**Regras relacionadas:** RN01, RN23

---

### US21 — Reintegrar uma conta banida · `Could Have` · `S` · Status: `⚪ Draft`

**Como** moderador, **eu quero** reintegrar uma conta banida **para que** quem foi banido por engano volte à rede.

**Critérios de aceite:**

- [ ] **1.** **Dado** que sou moderador, **quando** reintegro uma conta banida informando o motivo, **então** a pessoa volta a poder entrar e usar o sistema.
- [ ] **2.** **Dado** que a conta é reintegrada, **quando** ela volta, **então** os pedidos cancelados não voltam: ela faz novos e entra no fim da fila.
- [ ] **3.** **Dado** que a conta é reintegrada, **quando** ela volta, **então** os equipamentos que ficaram Extraviados continuam assim.
- [ ] **4.** **Dado** que não sou moderador, **quando** tento reintegrar, **então** a ação é recusada.

**Regras relacionadas:** RN10, RN21, RN23

---

### US22 — Análise automática de fotos · `Could Have` · `M` · Status: `⚪ Draft`

**Como** moderador, **eu quero** que as fotos sejam analisadas automaticamente **para que** o que for suspeito chegue a mim antes de aparecer.

**Critérios de aceite:**

- [ ] **1.** **Dado** que uma foto é enviada, **quando** a análise automática a considera suspeita, **então** ela fica oculta e vai para a análise de um moderador.
- [ ] **2.** **Dado** que a foto foi ocultada pela análise automática, **quando** o moderador a analisa, **então** pode permitir que apareça, mantê-la oculta ou banir a conta.
- [ ] **3.** **Dado** que a análise automática considera a foto adequada, **quando** ela é enviada, **então** aparece normalmente.

**Regras relacionadas:** RN06, RN23

---

### US23 — Ver o perfil de outra pessoa · `Could Have` · `S` · Status: `⚪ Draft`

**Como** usuário, **eu quero** ver o perfil de outra pessoa **para que** eu conheça quem doou e quem recebeu.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou logado, **quando** abro o perfil de outra pessoa, **então** vejo a foto de perfil, os equipamentos que ela doou e recebeu, e a quantidade de cada um.
- [ ] **2.** **Dado** que estou logado, **quando** adiciono ou troco a minha foto de perfil, **então** ela aparece no meu perfil.
- [ ] **3.** **Dado** que a pessoa escolheu ocultar uma parte do perfil, **quando** abro, **então** essa parte não aparece. Moderador e admin veem tudo.
- [ ] **4.** **Dado** que não estou logado, **quando** tento abrir um perfil, **então** o sistema me pede para entrar.

**Regras relacionadas:** RN20

---

### US24 — Configurar a minha privacidade · `Could Have` · `S` · Status: `⚪ Draft`

**Como** usuário, **eu quero** escolher como apareço **para que** eu controle o que os outros veem de mim.

**Critérios de aceite:**

- [ ] **1.** **Dado** que não mexi na configuração, **quando** apareço no histórico ou no painel, **então** apareço com o meu nome.
- [ ] **2.** **Dado** que escolho aparecer como anônimo, **quando** o histórico mostra uma mudança de posse minha, **então** aparece "usuário anônimo". Moderador e admin veem o nome real.
- [ ] **3.** **Dado** que sou anônimo, **quando** sou o dono ou estou com um equipamento, **então** meu nome continua aparecendo fora do histórico.
- [ ] **4.** **Dado** que sou anônimo, **quando** o painel mostra o meu pedido, **então** a mensagem não cita o meu nome (US06, critério 2).
- [ ] **5.** **Dado** que escolho quais partes do perfil exibir, **quando** outra pessoa o abre, **então** vê só essas partes.
- [ ] **6.** **Dado** que estou no chat, **quando** converso, **então** o meu nome aparece para o outro, mesmo que eu tenha escolhido ser anônimo.

**Regras relacionadas:** RN20

---

### US25 — Marcar um equipamento como extraviado · `Could Have` · `S` · Status: `⚪ Draft`

**Como** pessoa que está com um equipamento, **eu quero** informar que o perdi **para que** ele saia dos disponíveis.

**Critérios de aceite:**

- [ ] **1.** **Dado** que estou com um equipamento, **quando** informo que o perdi, **então** ele passa a **Extraviado** e sai dos disponíveis.
- [ ] **2.** **Dado** que sou moderador ou admin, **quando** marco um equipamento como Extraviado, **então** ele passa a Extraviado e sai dos disponíveis.
- [ ] **3.** **Dado** que um equipamento está Extraviado, **quando** o cruzamento procura, **então** ele não é reservado.
- [ ] **4.** **Dado** que um equipamento foi marcado como Extraviado, **quando** o histórico é consultado, **então** mostra o evento, com a data e quem estava com ele, sem motivos.
- [ ] **5.** **Dado** que não estou com o equipamento nem sou moderador ou admin, **quando** tento marcá-lo, **então** a ação é recusada.

**Regras relacionadas:** RN07, RN14

---

### US26 — Sub-fila de confirmação · `Could Have` · `M` · Status: `⚪ Draft`

**Como** solicitante que perdeu a vez, **eu quero** confirmar se ainda quero o equipamento **para que** eu não perca a minha posição por um contratempo.

**Critérios de aceite:**

- [ ] **1.** **Dado** que tenho a vez e passa 1 semana sem eu obter o equipamento, **quando** o prazo vence, **então** vou para a **sub-fila** em vez de ir para o fim, e o próximo passa a ter a vez.
- [ ] **2.** **Dado** que estou na sub-fila, **quando** chega a hora, **então** sou perguntado se ainda quero o equipamento e tenho **1 dia** para responder.
- [ ] **3.** **Dado** que respondo que **sim**, **quando** respondo, **então** volto à primeira posição.
- [ ] **4.** **Dado** que respondo que **não**, **quando** respondo, **então** saio da fila e o meu pedido vira Cancelado.
- [ ] **5.** **Dado** que **não respondo** em 1 dia, **quando** o prazo vence, **então** continuo na sub-fila e o próximo é perguntado.
- [ ] **6.** **Dado** que outro solicitante olha a fila, **quando** há alguém na sub-fila, **então** vê que essa pessoa está na frente dele, que tem 1 dia para responder e o motivo (inatividade).

**Regras relacionadas:** RN11, RN12

---

### US27 — Postar um agradecimento e fotos · `Could Have` · `S` · Status: `⚪ Draft`

**Como** solicitante, **eu quero** postar um agradecimento e fotos com o equipamento **para que** a rede veja que a ajuda funcionou.

**Critérios de aceite:**

- [ ] **1.** **Dado** que confirmei o recebimento, **quando** posto um agradecimento de até **255 caracteres**, **então** ele fica no meu pedido finalizado e aparece no carrossel do painel.
- [ ] **2.** **Dado** que confirmei o recebimento, **quando** envio fotos minhas com o equipamento (até 5), **então** elas aparecem no carrossel, só porque eu escolhi postá-las.
- [ ] **3.** **Dado** que escrevo mais de 255 caracteres, **quando** tento postar, **então** o sistema recusa e informa o limite.
- [ ] **4.** **Dado** que não confirmei o recebimento, **quando** tento postar, **então** o sistema recusa.
- [ ] **5.** **Dado** que já postei, **quando** decido apagar o agradecimento ou as fotos, **então** eles somem do carrossel.

**Regras relacionadas:** RN10, RN14

---

### US28 — Fornecedores disputam o fornecimento (pregão) · `Could Have` · `L` · Status: `⚪ Draft`

**Como** empresa de saúde fornecedora, **eu quero** disputar o fornecimento do equipamento do primeiro pedido, com lances de preço, **para** fornecer o próprio equipamento pelo valor que ofereci.

**Critérios de aceite:**

- [ ] **1.** **Dado** que há um pedido Pendente em primeiro lugar sem equipamento disponível, **quando** uma empresa de saúde dá um lance, **então** o lance fica registrado com o valor e a empresa.
- [ ] **2.** **Dado** que há vários lances, **quando** a disputa é encerrada, **então** vence o de menor valor.
- [ ] **3.** **Dado** que há um vencedor, **quando** a disputa termina, **então** o menor lance é o preço do pedido, e a empresa vencedora fornece o próprio equipamento por esse valor.
- [ ] **4.** **Dado** que não sou empresa de saúde fornecedora, **quando** tento dar um lance, **então** o sistema recusa.

**Regras relacionadas:** RN16

---

### US29 — Receber as contribuições e comprar o equipamento · `Could Have` · `M` · Status: `⚪ Draft`

**Como** recebedor, **eu quero** receber as contribuições, comprar o equipamento e emitir a nota **para que** o dinheiro vire equipamento.

**Critérios de aceite:**

- [ ] **1.** **Dado** que o caixa cobre o preço do pedido em primeiro lugar, **quando** sou avisado, **então** o valor fica disponível para eu comprar o equipamento.
- [ ] **2.** **Dado** que comprei o equipamento, **quando** registro a compra com a nota, **então** o valor sai do caixa e a nota fica guardada.
- [ ] **3.** **Dado** que não sou recebedor, **quando** tento registrar uma compra, **então** a ação é recusada.

**Regras relacionadas:** RN15, RN16

---

## 🛡️ 5. Regras de Negócio (Constraints)

| ID | Regra |
| :-- | :---- |
| RN01 | O e-mail de login é único e só muda por pedido analisado por um moderador |
| RN02 | Só nome e e-mail de login são obrigatórios no cadastro. Os demais dados são opcionais |
| RN03 | Só equipamentos médicos entram na rede. A moderação recusa o resto |
| RN04 | Os equipamentos vêm de uma lista. Um equipamento ou tipo novo entra por proposta aprovada por moderador ou admin, que define os campos customizáveis |
| RN05 | Cada equipamento é cadastrado individualmente, sem campo de quantidade geral. O campo customizável (exemplo: Uma ou Par nas muletas) aparece com clareza na entrega |
| RN06 | Todo equipamento tem de 1 a 5 fotos |
| RN07 | Estados: Danificado, Utilizável, Perfeito estado e Novo. Danificado exige descrição. **Extraviado** não é escolhido no cadastro: é aplicado pelo sistema, por um moderador ou por quem o perdeu |
| RN08 | Um equipamento está disponível quando ninguém o usa: está no centro, ou uma pessoa o disponibilizou e ninguém o pegou |
| RN09 | O pedido não escolhe o estado e recebe sempre Utilizável ou melhor. Uma pessoa pode ter vários pedidos abertos |
| RN10 | Status do pedido: Pendente, Em processo de compra, Finalizado, Recusado e Cancelado |
| RN11 | A fila é por o que se precisa (equipamento e filtros). Vale o pedido mais antigo, e o equipamento entregue é o disponível mais antigo que atende. O cruzamento é automático |
| RN12 | Quem tem a vez tem 1 semana para obter o equipamento. Depois, vai para o fim da fila |
| RN13 | Um pedido recusado tem um só motivo (o primeiro), e um motivo pode servir a vários pedidos. O dono do pedido vê o motivo |
| RN14 | Posse é o registro de quem está com o equipamento. Emprestar vale como doar, e a plataforma não garante nem cobra a devolução. A posse passa quando o solicitante confirma o recebimento |
| RN15 | O caixa é único. Cada contribuição (mínimo R$ 5,00) só entra depois de confirmada. O caixa nunca fica negativo |
| RN16 | O admin define o preço, que pode mudar. Quando o caixa cobre o preço, o pedido vai a Em processo de compra. A compra é feita fora do site e registrada com o valor pago, que sai do caixa |
| RN17 | Se o valor real for maior que o caixa, o admin devolve o pedido a Pendente |
| RN18 | Quem não tem conta não vê nada no sistema |
| RN19 | Os horários em que a pessoa está em casa e o endereço completo só a outra parte vê. A cidade e o sim ou não de entregar ou buscar são visíveis a todos os usuários logados |
| RN20 | O padrão é aparecer com o nome. O anonimato vale só no histórico e no painel. Quem cadastra ou está com o equipamento aparece sempre, e no chat o nome aparece. Moderador e admin veem sempre |
| RN21 | Banido não faz nada: sai da fila (pedidos Cancelados), e o que estava com ele fica Extraviado. Se voltar, entra no fim da fila |
| RN22 | A conta excluída tem os dados guardados por 6 meses. O tratamento de dados segue a LGPD |
| RN23 | Moderador não modera outro moderador nem caso em que é parte (exceto recusar o próprio pedido). O admin modera os moderadores e faz tudo o que os outros fazem |
| RN24 | Cada conversa é entre solicitante, doador e um equipamento. Mudou o equipamento, é outra conversa |
| RN25 | Os dados de cadastro podem ser entregues à Justiça, como a lei determinar |
| RN26 | Os dados de contato da conta (telefones, WhatsApp, e-mail de contato) não são exibidos à outra parte. Quem quiser compartilhar o telefone faz isso no chat, por conta e risco |

---

## 🚫 6. Fora de Escopo (Non-goals)

> O que o produto deliberadamente **não** faz neste semestre — o `Won't Have`
> do MoSCoW, com o motivo de cada corte.

- **Controle e cobrança de devolução:** emprestar vale como doar, e isso não é responsabilidade da plataforma.
- **Conserto de equipamento:** quem pega um equipamento danificado decide se o conserta.
- **Recurso de banimento:** é tratado por e-mail, fora do site.
- **Recuperação de equipamento extraviado:** é caso de justiça.
- **Troca ou doação de itens que não são equipamentos médicos:** o aplicativo é médico.
- **Compra, nota fiscal e prestação de contas pelo admin:** ele faz fora do site, até a US29, para não criar um papel novo e atrasar as specs.
- **Dinheiro real:** o pagamento roda só em ambiente de testes, como a disciplina exige.

---

## ⚙️ 7. Requisitos Não Funcionais (Qualidade)

> Só os que você consegue justificar na defesa.

- **Privacidade:** as conversas só são lidas pelas duas partes, e os dados pessoais seguem a LGPD.
- **Autenticidade:** uma mensagem denunciada carrega a prova de que não foi forjada.
- **Acesso:** só quem tem conta usa o sistema, e as ações de moderação, gestão e admin ficam restritas a cada papel.
- **Pagamento:** a contribuição só vale depois de confirmada, e o dinheiro nunca é contado antes disso.
- **Rastreabilidade:** o histórico do equipamento e o motivo de cada banimento e recusa ficam registrados.

---

## ❓ Dúvidas em aberto

| # | Dúvida | Onde resolver |
| :-- | :----- | :------------ |
| 1 | O tema é único na turma (afirmado pelo aluno), mas o aceite do professor ainda está pendente. Nenhuma Issue nasce sem ele | Aceite do professor |
| 2 | A contribuição financeira vale como o fluxo de pagamento da ficha, já que o pedido e o pagamento não se ligam diretamente (o caixa é único e independente do pedido)? | Aceite do professor |

---

## 🛠️ 8. Histórico

| Data | Versão | O que mudou |
| :--- | :----- | :---------- |
| 2026-09-21 | 1.0.0 | Versão inicial via `/utf-prd` |
