# Local Mail Plugin for Moodle (Versão Customizada / Modificada)

Esta é uma **versão modificada e aprimorada** do plugin oficial [Local Mail para Moodle](https://moodle.org/plugins/local_mail). 

O plugin original oferece uma interface de e-mail web moderna para comunicação entre participantes de um mesmo curso. Esta versão estende o projeto original ao introduzir **mensageria global em todo o site (nível de sistema)** para todos os usuários autenticados, além de incluir correções críticas de banco de dados, busca refinada de usuários e melhorias de resiliência na interface com gerenciador de anexos.

---

## 🌟 Principais Diferenças e Melhorias em Relação ao Projeto Original

### 1. 📢 Mensageria Global em Nível de Sistema (`SITEID`)
* **Projeto Original:** A comunicação era estritamente restrita aos cursos nos quais os usuários estavam matriculados (`courseid > 1`).
* **Versão Modificada:** Integra o contexto de Sistema do Moodle (`SITEID = 1`). Todos os usuários autenticados no Moodle agora podem enviar e receber mensagens globais sem a necessidade de estarem matriculados em um curso específico, mantendo a opção de filtrar mensagens por curso quando desejado.

### 2. 🔍 Busca de Usuários Global Refinada
* **Projeto Original:** A busca de destinatários realizava junções diretas com tabelas de matrícula (`user_enrolments`), impossibilitando a localização de usuários em nível global.
* **Versão Modificada:** Em buscas no contexto global (`SITEID`), as junções de matrícula são ignoradas. O filtro de busca exclui automaticamente:
  * O próprio usuário remetente.
  * Usuários suspensos, excluídos ou não confirmados.
  * O usuário visitante/convidado (`$CFG->siteguest`).

### 3. 🛡️ Exclusão Segura de Dados do Curso (`delete_course_data`)
* **Projeto Original:** A função `delete_course_data()` possuía uma subconsulta genérica que apagava referências de mensagens de forma global, afetando acidentalmente threads e mensagens de outros cursos.
* **Versão Modificada:** Restaurado o filtro estrito `WHERE courseid = :courseid` em `classes/message.php`, garantindo que a exclusão de dados de um curso não afete threads ou mensagens globais/de outros cursos.

### 4. 🧹 Remoção de Cursos Duplicados na Interface
* **Projeto Original:** A listagem de preferências e o menu suspenso de cursos poderiam exibir entradas duplicadas do contexto de Sistema (`SITEID`).
* **Versão Modificada:** O método `get_preferences_raw()` em `classes/external.php` higieniza a lista de cursos, garantindo que o curso do Sistema (`coresystem`) apareça de forma única e organizada na UI.

### 5. 📎 Resiliência no Gerenciador de Anexos (Filemanager SPA)
* **Projeto Original:** A inicialização do formulário via AJAX em uma aplicação de página única (Svelte SPA) podia travar o container de anexos no estado de carregamento infinito (`fm-loading`), devido a ouvintes `Y.on('domready')` não disparados após o carregamento inicial da página ou exceções lançadas por editores rich text de terceiros (ex: `tiny_htmlbootstrapeditor` / `M.recit`).
* **Versão Modificada:** 
  * Injetados stubs de salvaguarda JavaScript para `window.M.recit`, evitando que scripts assíncronos interrompam a inicialização do formulário.
  * Implementada a verificação dinâmica do estado do documento `(document.readyState === "complete" || document.readyState === "interactive")`, permitindo que o `M.form_filemanager.init` execute instantaneamente em requisições AJAX do SPA.

---

## 🚀 Instalação

1. Baixe ou clone este repositório no diretório do seu Moodle:
   ```bash
   /caminho/para/moodle/local/mail
   ```
2. Acesse a administração do seu Moodle (**Administração do site > Notificações**) para concluir a instalação da extensão.

---

## 🛠️ Requisitos
* **Moodle:** 4.x ou 5.x
* **PHP:** Versão compatível com a sua instalação Moodle (PHP 8.1+)

---

## 📄 Licença e Direitos Autorais

Este plugin é distribuído sob os termos da licença **GNU General Public License v3.0 or later**.

### Créditos do Projeto Original:
* © 2012-2014 Institut Obert de Catalunya
* © 2014-2023 Marc Català <reskit@gmail.com>
* © 2016-2025 Albert Gasset <albertgasset@fsfe.org>
* © 2023-2024 Proyecto UNIMOODLE
