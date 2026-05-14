# Sistema de Gestão das Olimpíadas (SGO)

> **PUC Minas — Engenharia de Software**
> **Disciplina:** Projeto de Software (4º período)
> **Professor:** João Paulo Carneiro Aramuni
> **Trabalho 1 — Primeira Entrega — Valor: 10 pontos**
>
> **Autor:** Luiz Fernando Batista Moreira
> **Matrícula:** 869239

---

## 1. Descrição do Sistema

Com a chegada das Olimpíadas, um novo sistema de gestão é necessário para coordenar os diferentes aspectos do evento. O **Sistema de Gestão das Olimpíadas (SGO)** permite o gerenciamento de **competições**, **inscrições de atletas**, **alocação de locais** para as provas e o **controle de resultados**, gerando ainda **relatórios de medalhas** por país.

---

## 2. Regras de Negócio

1. **Cadastro de competições:** o sistema deve permitir o cadastro de competições, que incluem o nome da modalidade, data, horário, local e lista de atletas inscritos.
2. **Inscrição de atletas:** atletas de diferentes países devem se inscrever em competições específicas. Cada atleta pode participar de várias competições, mas só pode representar um país em cada modalidade.
3. **Alocação de locais:** os locais para as competições devem ser alocados de forma a evitar conflitos de horário. Um local só pode abrigar uma competição por vez.
4. **Controle de resultados:** após a realização das competições, os resultados devem ser registrados, determinando o atleta vencedor e os classificados em segundo e terceiro lugares.
5. **Relatórios de medalhas:** o sistema deve gerar relatórios de medalhas, mostrando o desempenho de cada país com base nas medalhas de ouro, prata e bronze conquistadas.

---

## 3. Histórias de Usuário

Cada história segue o padrão **Como [ator], quero [ação], para que [valor]**, acompanhada de **Critérios de Aceitação** no formato Given/When/Then (GWT).

---

#### US01 — Cadastrar Competição
**Como** Administrador, **quero** cadastrar uma nova competição informando modalidade, data, horário e local, **para que** o evento seja oficialmente programado na agenda olímpica.

**Critérios de Aceitação:**
- **Dado** que sou um Administrador autenticado, **quando** informo modalidade, data, horário e local válidos e confirmo o cadastro, **então** a competição é criada com status `AGENDADA`.
- **Dado** que o local informado já está reservado para outra competição no mesmo horário, **quando** tento salvar, **então** o sistema rejeita o cadastro exibindo a mensagem "Conflito de horário no local selecionado".

---

#### US02 — Editar/Cancelar Competição
**Como** Administrador, **quero** editar ou cancelar uma competição já cadastrada, **para que** eu possa corrigir erros ou tratar imprevistos antes da realização da prova.

**Critérios de Aceitação:**
- **Dado** que a competição está com status `AGENDADA`, **quando** edito seus dados ou a cancelo, **então** a alteração é persistida e os atletas inscritos são notificados.
- **Dado** que a competição já está `ENCERRADA`, **quando** tento editá-la, **então** o sistema impede a alteração.

---

#### US03 — Alocar Local
**Como** Administrador, **quero** alocar um local a uma competição garantindo que não haja conflito de horário com outro evento no mesmo local, **para que** a infraestrutura seja utilizada sem sobreposições.

**Critérios de Aceitação:**
- **Dado** que o local está disponível na data/hora solicitadas, **quando** confirmo a alocação, **então** a reserva é registrada e o local fica indisponível para o mesmo intervalo.
- **Dado** que há sobreposição com outra competição no mesmo local, **quando** tento alocar, **então** o sistema rejeita a operação e sugere horários alternativos.

---

#### US04 — Cadastrar Atletas e Países
**Como** Administrador, **quero** cadastrar atletas e países participantes, **para que** eles fiquem disponíveis para inscrição nas competições.

**Critérios de Aceitação:**
- **Dado** que informo nome, data de nascimento, gênero e país de origem, **quando** salvo o atleta, **então** ele passa a integrar a lista de participantes elegíveis.
- **Dado** que o país já existe, **quando** tento cadastrá-lo novamente, **então** o sistema retorna erro de duplicidade.

---

#### US05 — Inscrever Atleta
**Como** Atleta, **quero** me inscrever em uma competição representando o meu país, **para que** eu possa participar oficialmente da modalidade.

**Critérios de Aceitação:**
- **Dado** que sou um atleta autenticado e a competição está com status `AGENDADA`, **quando** confirmo minha inscrição, **então** uma `Inscricao` é gerada com status `PENDENTE`.
- **Dado** que já tenho uma inscrição confirmada em outra competição **da mesma modalidade** representando outro país, **quando** tento me inscrever, **então** o sistema bloqueia a operação (RN2).

---

#### US06 — Validar Regra "Um país por modalidade"
**Como** Sistema, **quero** impedir que um mesmo atleta represente mais de um país em uma mesma modalidade, **para que** a regra olímpica seja respeitada.

**Critérios de Aceitação:**
- **Dado** que o atleta possui inscrição prévia confirmada em uma modalidade representando o país X, **quando** for criada uma nova inscrição na mesma modalidade representando o país Y, **então** o sistema deve rejeitar com a mensagem "Atleta já representa outro país nesta modalidade".

---

#### US07 — Registrar Resultado
**Como** Árbitro/Juiz, **quero** registrar o resultado de uma competição informando 1º, 2º e 3º lugares, **para que** o pódio seja oficializado e as medalhas atribuídas.

**Critérios de Aceitação:**
- **Dado** que a competição está `EM_ANDAMENTO` ou `ENCERRADA`, **quando** informo os três classificados, **então** o sistema atribui automaticamente Ouro, Prata e Bronze e atualiza o quadro de medalhas.
- **Dado** que tento registrar mais de três posições para a mesma competição, **quando** confirmo, **então** o sistema rejeita a operação (RN4).

---

#### US08 — Gerar Relatório de Medalhas
**Como** Administrador, **quero** gerar relatório de medalhas por país (ouro, prata e bronze), **para que** o desempenho de cada delegação seja divulgado de forma clara.

**Critérios de Aceitação:**
- **Dado** que existem resultados registrados, **quando** solicito o relatório, **então** o sistema gera um quadro contendo cada país com a soma de medalhas por tipo, ordenado pelo critério olímpico (ouro > prata > bronze).
- **Dado** que solicito a exportação, **quando** confirmo, **então** o relatório é disponibilizado em PDF.

---

#### US09 — Consultar Programação
**Como** Público, **quero** consultar a programação das competições, **para que** eu possa acompanhar dias, horários e locais dos eventos.

**Critérios de Aceitação:**
- **Dado** que existem competições agendadas, **quando** acesso a página de programação, **então** vejo a lista com modalidade, data, horário e local, podendo filtrar por dia ou modalidade.

---

#### US10 — Consultar Quadro de Medalhas
**Como** Público, **quero** consultar o quadro geral de medalhas, **para que** eu acompanhe em tempo real o desempenho dos países participantes.

**Critérios de Aceitação:**
- **Dado** que há resultados registrados, **quando** acesso o quadro de medalhas, **então** o sistema exibe o ranking atualizado de países com totais de ouro, prata e bronze.

---

#### US11 — Visualizar Minhas Inscrições e Resultados
**Como** Atleta, **quero** visualizar minhas inscrições confirmadas e meus resultados, **para que** eu acompanhe minha participação nos jogos.

**Critérios de Aceitação:**
- **Dado** que sou um atleta autenticado, **quando** acesso minha área pessoal, **então** vejo a lista das minhas inscrições (com status) e dos meus resultados (com posição e medalha, quando aplicável).

---

#### US12 — Autenticar Usuário
**Como** Usuário do sistema, **quero** autenticar-me com login e senha, **para que** apenas perfis autorizados acessem funcionalidades restritas (cadastros, alocações e resultados).

**Critérios de Aceitação:**
- **Dado** que informo login e senha válidos, **quando** confirmo o acesso, **então** o sistema concede sessão autenticada conforme o meu perfil (Administrador, Árbitro ou Atleta).
- **Dado** que informo credenciais inválidas três vezes consecutivas, **quando** tento autenticar novamente, **então** o sistema bloqueia o acesso temporariamente.

---

## 4. Diagramas UML

Todos os diagramas foram modelados em **PlantUML**. Os arquivos-fonte estão em [`codigos/`](codigos/) e as imagens geradas em [`imagens/`](imagens/).

### 4.1 Diagrama de Caso de Uso

Modela os atores (**Administrador**, **Atleta**, **Árbitro/Juiz** e **Público**) e os casos de uso principais — incluindo *Cadastrar Competição*, *Alocar Local*, *Inscrever Atleta*, *Registrar Resultado* e *Gerar Relatório de Medalhas* — com relações `<<include>>` e `<<extend>>` cobrindo as regras de negócio.

<img width="900px" src="imagens/diagrama-de-caso-de-uso.png" alt="Diagrama de Caso de Uso"/>

> Código-fonte: [`codigos/diagrama-de-caso-de-uso.puml`](codigos/diagrama-de-caso-de-uso.puml)

---

### 4.2 Diagrama de Classes

Representa a estrutura estática do domínio, com as classes **Competição**, **Atleta**, **País**, **Local**, **Modalidade**, **Inscrição** (classe associativa entre Atleta e Competição), **Resultado**, **RelatórioMedalhas** e **Usuário**, além dos enums `TipoMedalha`, `StatusCompeticao` e `StatusInscricao`. As restrições de regra de negócio (1 país por modalidade, sem conflito de horário e três posições por competição) estão registradas em notas UML.

<img width="900px" src="imagens/diagrama-de-classes.png" alt="Diagrama de Classes"/>

> Código-fonte: [`codigos/diagrama-de-classes.puml`](codigos/diagrama-de-classes.puml)

---

### 4.3 Diagrama de Pacotes

Organiza o sistema em **quatro camadas** seguindo o estilo de arquitetura em camadas:

- `br.pucminas.sgo.apresentacao` — interfaces de usuário (views).
- `br.pucminas.sgo.aplicacao` — serviços/controllers (regras de aplicação).
- `br.pucminas.sgo.dominio` — entidades e regras de negócio.
- `br.pucminas.sgo.infraestrutura` — repositórios de persistência e integrações externas.

<img width="900px" src="imagens/diagrama-de-pacotes.png" alt="Diagrama de Pacotes"/>

> Código-fonte: [`codigos/diagrama-de-pacotes.puml`](codigos/diagrama-de-pacotes.puml)

---

### 4.4 Diagrama de Componentes

Modela os componentes principais do sistema — **Interface de Usuário**, **Módulo de Autenticação**, **Módulo de Competições**, **Módulo de Inscrições**, **Módulo de Alocação**, **Módulo de Resultados** e **Módulo de Relatórios** — com as interfaces fornecidas/requeridas e as conexões com o banco de dados e o serviço de e-mail.

<img width="900px" src="imagens/diagrama-de-componentes.png" alt="Diagrama de Componentes"/>

> Código-fonte: [`codigos/diagrama-de-componentes.puml`](codigos/diagrama-de-componentes.puml)

---

### 4.5 Diagrama de Implantação

Ilustra a arquitetura física do SGO: **dispositivos dos usuários** (navegador web e app mobile), **servidor web/proxy reverso (Nginx)**, **servidor de aplicação** (contêiner Docker com a API Spring Boot e o front-end), **servidor de banco de dados PostgreSQL**, **servidor de relatórios** e o **serviço de e-mail externo**, com os respectivos protocolos de comunicação.

<img width="900px" src="imagens/diagrama-de-implantacao.png" alt="Diagrama de Implantação"/>

> Código-fonte: [`codigos/diagrama-de-implantacao.puml`](codigos/diagrama-de-implantacao.puml)

---

## 5. Estrutura do Repositório

```
Trabalho-SGO/
├── README.md
├── Trabalho - SGO - 10 pontos-1 (2).pdf
├── imagens/
│   ├── diagrama-de-caso-de-uso.png
│   ├── diagrama-de-classes.png
│   ├── diagrama-de-pacotes.png
│   ├── diagrama-de-componentes.png
│   └── diagrama-de-implantacao.png
└── codigos/
    ├── diagrama-de-caso-de-uso.puml
    ├── diagrama-de-classes.puml
    ├── diagrama-de-pacotes.puml
    ├── diagrama-de-componentes.puml
    └── diagrama-de-implantacao.puml
```

---

## 6. Tecnologias Utilizadas

- **[PlantUML](https://plantuml.com/)** — linguagem para modelagem dos diagramas UML.
- **[PlantUML Web Server](https://www.plantuml.com/plantuml/)** — utilizado para renderização das imagens PNG a partir dos arquivos `.puml`.

---

## 7. Como Regerar as Imagens

A forma mais simples é colar o conteúdo de cada arquivo `.puml` no editor online oficial:

- https://www.plantuml.com/plantuml/uml/

Alternativas:

- **Extensão PlantUML do VSCode** (`Alt + D` para preview e `Ctrl + Shift + P → PlantUML: Export Current Diagram`).
- **CLI local** (requer Java): `java -jar plantuml.jar codigos/*.puml -o ../imagens`.

---

## 8. Referências

### Enunciado e Materiais da Disciplina
- ARAMUNI, J. P. C. **Enunciado do Trabalho 1 — Projeto de Software (SGO).** PUC Minas, 2026. (incluso no repositório como `Trabalho - SGO - 10 pontos-1 (2).pdf`)
- ARAMUNI, J. P. C. **Projeto de Software — Repositório de Apoio.** Disponível em: https://github.com/joaopauloaramuni/projeto-de-software

### Bibliografia UML e Engenharia de Software
- BOOCH, G.; RUMBAUGH, J.; JACOBSON, I. **UML: Guia do Usuário.** 2. ed. Rio de Janeiro: Elsevier, 2006.
- FOWLER, M. **UML Essencial: Um Breve Guia para a Linguagem-Padrão de Modelagem de Objetos.** 3. ed. Porto Alegre: Bookman, 2014.
- SOMMERVILLE, I. **Engenharia de Software.** 10. ed. São Paulo: Pearson, 2019.
- PRESSMAN, R. S.; MAXIM, B. R. **Engenharia de Software: Uma Abordagem Profissional.** 8. ed. Porto Alegre: AMGH, 2016.
- LARMAN, C. **Utilizando UML e Padrões: Uma Introdução à Análise e ao Projeto Orientados a Objetos e ao Desenvolvimento Iterativo.** 3. ed. Porto Alegre: Bookman, 2007.
- COHN, M. **User Stories Applied: For Agile Software Development.** Boston: Addison-Wesley, 2004.

### Padrões e Especificações
- OBJECT MANAGEMENT GROUP. **OMG Unified Modeling Language (UML) Specification, Version 2.5.1.** Disponível em: https://www.omg.org/spec/UML/2.5.1/

### Ferramentas
- **PlantUML — Guia Oficial:** https://plantuml.com/guide
- **PlantUML — Documentação de Diagramas:** https://plantuml.com/
- **PlantUML Web Server (renderização online):** https://www.plantuml.com/plantuml/uml/
