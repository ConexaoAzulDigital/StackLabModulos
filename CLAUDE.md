# StackLabModulos — Guia para Agentes

> **Produto:** Mágica Chat (Chatwoot + Kanban para Chatwoot)
> **Empresa:** Conexão Azul Digital — conexaoazul.com
> **Instância:** magicachat.conexaoazul.com
> **Branch de desenvolvimento:** `claude/todo-implementation-2pafu3`

Este repositório contém **Dashboard Scripts** — fragmentos HTML/JS/CSS injetados
pelo Chatwoot no final de cada página do dashboard. Eles personalizam aparência e
comportamento sem tocar no código-fonte do Chatwoot.

---

## 1. Como os Dashboard Scripts funcionam

```
Chatwoot Dashboard (Vue 3 SPA)
  └─ <body>
       └─ [... app Vue ...]
       └─ <!-- dashboard scripts injetados aqui -->
            └─ <script data-name="MeuScript">...</script>
```

- Cada script é um bloco `<script>` autônomo inserido via
  **Super Admin → Dashboard Scripts → New Script**
- O Chatwoot injeta todos os scripts habilitados em **todas** as páginas
- A aplicação é uma **SPA Vue 3** com Vuex; o DOM muda dinamicamente
- Use sempre `MutationObserver` para garantir que customizações persistam
- Use um `MODULE_KEY` global para evitar dupla execução (`if (window[KEY]) return`)

### Acessar o estado Vue (Vuex Store)

```js
const vue   = window.__vue__;
const store = vue?.$store;
const user  = store?.getters?.getCurrentUser;        // agente logado
const items = store?.getters['kanban/getKanbanItems']; // itens do Kanban
```

### Padrão canônico de script

```html
<script data-name="NomeDoScript">
(function () {
  const KEY = 'MagicaChat_NomeDoScript_v1';
  if (window[KEY]) return;          // idempotência
  window[KEY] = true;

  function aplicar() {
    // sua lógica aqui
  }

  aplicar();
  new MutationObserver(aplicar).observe(document.body, {
    childList: true, subtree: true
  });
})();
</script>
```

---

## 2. Brand Identity — Conexão Azul Digital

> **Fonte da verdade:** `conexaoazul/site-conexao-azul-digital/colors_and_type.css`
> (disponível em https://www.conexaoazul.com/colors_and_type.css)

### Paleta de cores

| Token | Hex | Uso |
|---|---|---|
| `--indigo-950` | `#0B1133` | fundo escuro, sidebar primária |
| `--indigo-800` | `#1E2D78` | cor primária do wordmark |
| `--indigo-700` | `#2A3D9B` | hover de links |
| `--cyan-500`   | `#2DD2E1` | **acento principal** — CTAs, badges, foco |
| `--cyan-400`   | `#5BDDE9` | hover do acento |
| `--neutral-900`| `#14182A` | sidebar secundária |
| `--neutral-600`| `#525A75` | texto secundário |
| `--success-500`| `#16A571` | estados de sucesso |
| `--danger-500` | `#DC4848` | erros e alertas |

### Tipografia

| Papel | Família | Peso |
|---|---|---|
| Display / Headings | **Bricolage Grotesque** | 600–800 |
| Body / UI | **Inter** | 400–600 |
| Código / IDs | **JetBrains Mono** | 400–500 |

Google Fonts import:
```css
@import url("https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,400;12..96,500;12..96,600;12..96,700;12..96,800&family=Inter:wght@400;500;600;700&family=JetBrains+Mono:wght@400;500;600&display=swap");
```

### Assets de logo (Cloudflare Pages)

| Arquivo | URL | Quando usar |
|---|---|---|
| `logo-light.svg` | `https://www.conexaoazul.com/logo-light.svg` | fundo escuro (sidebar) |
| `logo-dark.svg`  | `https://www.conexaoazul.com/logo-dark.svg`  | fundo claro |
| `logo-mark.png`  | `https://www.conexaoazul.com/logo-mark.png`  | ícone isolado |
| `favicon.svg`    | `https://www.conexaoazul.com/favicon.svg`    | aba do browser |

### Mapeamento para variáveis CSS do Chatwoot

O Chatwoot usa `--color-woot-{50..900}` e `--color-primary-medium` (HSL).
Mapear o **cyan** como cor de ação primária:

```css
:root {
  --color-woot-50:  #F2FCFD;
  --color-woot-100: #BFF2F7;
  --color-woot-200: #8EE8F0;
  --color-woot-300: #5BDDE9;
  --color-woot-400: #2DD2E1;   /* ← cyan-500, brand accent */
  --color-woot-500: #1FBDD0;
  --color-woot-600: #14A0B1;
  --color-woot-700: #0F7884;
  --color-woot-800: #0A4F58;
  --color-woot-900: #063540;
  --color-primary-medium: 186deg 79% 52%; /* HSL de #2DD2E1 */
}
```

---

## 3. Scripts existentes

### `examples/branding-magicachat.html` ✅ PRONTO
**Instalar como:** `MagicaChat-Branding`

O que faz:
- Injeta paleta de cores real da Conexão Azul Digital
- Aplica Bricolage Grotesque em headings, Inter no body
- Substitui logo Chatwoot por `logo-light/dark.svg` (com fallback texto)
- Atualiza favicon e título da aba: "Chatwoot" → "Mágica Chat"
- Sidebar indigo-950 com glow cyan em itens ativos
- Botões primários em cyan com texto indigo
- Scrollbar discreta em cyan
- Borda cyan no topo das colunas do Kanban
- Respeita `prefers-reduced-motion`

### `examples/call_button.html` ✅ PRONTO
**Instalar como:** `MagicaChat-CallButton`

O que faz:
- Botão flutuante verde com modal para iniciar ligações
- Validação de telefone (8–15 dígitos)
- Toast de confirmação animado (sem `alert()`)
- Fechar com `Escape` ou clique fora do modal
- Limpa o campo após ligar

**Para integrar com FreeSWITCH ou Asterisk:**
Substitua o trecho `mostrarToast(...)` por um `fetch()` para o middleware:
```js
await fetch('https://SEU-SERVIDOR:3001/originate/freeswitch', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ telefone, ramal: await getRamalDoAgente() }),
});
```

### `examples/mobile.html` ✅ PRONTO
**Instalar como:** `MagicaChat-Mobile`

O que faz:
- Navbar inferior para mobile (< 768px) com ícones Remix Icons
- Navegação: Conversas, Contatos, Kanban, Relatórios, Ajustes
- Oculta `<aside>` (sidebar desktop) no mobile
- Aplica padding adequado para não sobrepor conteúdo
- Customiza botão de voltar e botão de IA no mobile
- Força dark theme no mobile

### `examples/hide-all` ✅ PRONTO
**Instalar como:** `MagicaChat-OcultarAbaTodos`

O que faz:
- Busca perfil do agente via `/api/v1/profile`
- Se **não for admin**: oculta a aba "Todos" / "All Contacts"
- Se **for admin**: mantém a aba visível
- Suporta labels em PT e EN

### `examples/demo-loading.html` — Referência
Tela de loading animada com barra de progresso e etapas. Usar como base
para criar telas de splash/loading customizadas.

### `examples/demo-onboarding.html` — Referência
Fluxo de onboarding em 4 etapas com animações de transição. Usar como
base para criar experiências de boas-vindas para novos agentes.

### `examples/demo-success-notification.html` — Referência
Notificação de sucesso com ícone animado, detalhes e opção
"não mostrar novamente" via `localStorage`.

---

## 4. Receitas para operações comuns

### 4.1 Adicionar um botão flutuante

```js
function adicionarBotao({ id, icone, cor, tooltip, onClick }) {
  if (document.getElementById(id)) return;

  const style = document.createElement('style');
  style.innerHTML = `
    #${id} {
      position: fixed; bottom: 80px; right: 20px;
      width: 48px; height: 48px; border-radius: 50%;
      background: ${cor}; color: white;
      display: flex; align-items: center; justify-content: center;
      cursor: pointer; z-index: 9999;
      box-shadow: 0 4px 12px rgba(0,0,0,0.2);
      transition: transform 0.2s, box-shadow 0.2s;
    }
    #${id}:hover { transform: scale(1.1); }
  `;
  document.head.appendChild(style);

  const btn = document.createElement('div');
  btn.id = id;
  btn.title = tooltip;
  btn.innerHTML = icone; // SVG string
  btn.addEventListener('click', onClick);
  document.body.appendChild(btn);
}
```

### 4.2 Adicionar item à sidebar

```js
function adicionarItemSidebar({ icon, label, route }) {
  const sidebar = document.querySelector('.primary-sidebar ul, .primary-sidebar nav');
  if (!sidebar || sidebar.querySelector(`[data-mc-item="${label}"]`)) return;

  const item = document.createElement('li');
  item.dataset.mcItem = label;
  item.innerHTML = `
    <a href="#" style="display:flex;align-items:center;gap:10px;
      padding:10px 12px;color:rgba(255,255,255,0.7);
      text-decoration:none;border-radius:8px;font-size:14px;
      transition:all 0.15s ease;">
      ${icon}
      <span>${label}</span>
    </a>
  `;
  item.querySelector('a').addEventListener('click', e => {
    e.preventDefault();
    const id = getAccountId();
    if (id) window.location.href = `/app/accounts/${id}/${route}`;
  });
  sidebar.appendChild(item);
}

function getAccountId() {
  return window.location.pathname.match(/\/app\/accounts\/(\d+)/)?.[1];
}
```

### 4.3 Ocultar elemento por seletor CSS

```js
function ocultarElementos(seletores) {
  const style = document.createElement('style');
  style.innerHTML = seletores.map(s => `${s} { display: none !important; }`).join('\n');
  document.head.appendChild(style);
}

// Uso:
ocultarElementos([
  '.reports-link',           // link de relatórios para não-supervisores
  '[data-key="settings"]',   // menu de configurações para agentes
]);
```

### 4.4 Mostrar toast de notificação

```js
function toast(mensagem, tipo = 'info', duracao = 4000) {
  const cores = {
    info:    { bg: '#1E2D78', icon: 'ℹ' },
    success: { bg: '#16A571', icon: '✓' },
    warning: { bg: '#E8A23A', icon: '⚠' },
    error:   { bg: '#DC4848', icon: '✕' },
  };
  const { bg, icon } = cores[tipo] || cores.info;

  const el = document.createElement('div');
  el.style.cssText = `
    position:fixed; bottom:80px; right:20px;
    background:${bg}; color:white;
    padding:12px 16px; border-radius:10px;
    font-family:Inter,sans-serif; font-size:14px; font-weight:500;
    display:flex; align-items:center; gap:10px;
    box-shadow:0 4px 16px rgba(0,0,0,0.25);
    z-index:10001; opacity:0;
    transition:opacity 0.25s, transform 0.25s;
    transform:translateY(10px);
  `;
  el.innerHTML = `<span>${icon}</span><span>${mensagem}</span>`;
  document.body.appendChild(el);

  requestAnimationFrame(() => { el.style.opacity='1'; el.style.transform='translateY(0)'; });
  setTimeout(() => {
    el.style.opacity='0';
    el.style.transform='translateY(10px)';
    setTimeout(() => el.remove(), 300);
  }, duracao);
}
```

### 4.5 Fluxo de onboarding com steps

```js
function iniciarOnboarding(steps) {
  // steps: [{ titulo, descricao, elemento (seletor a destacar), acao }]
  let step = 0;

  function renderStep() {
    document.getElementById('mc-onboarding')?.remove();

    if (step >= steps.length) {
      localStorage.setItem('mc_onboarding_done', '1');
      return;
    }

    const { titulo, descricao, elemento } = steps[step];
    const alvo = elemento ? document.querySelector(elemento) : null;
    if (alvo) {
      alvo.scrollIntoView({ behavior: 'smooth', block: 'center' });
      alvo.style.outline = `3px solid #2DD2E1`;
      alvo.style.borderRadius = '6px';
    }

    const popup = document.createElement('div');
    popup.id = 'mc-onboarding';
    popup.style.cssText = `
      position:fixed; bottom:90px; left:50%; transform:translateX(-50%);
      background:#0B1133; color:white; padding:20px 24px;
      border-radius:14px; border:1px solid rgba(45,210,225,0.25);
      width:340px; z-index:10002; font-family:Inter,sans-serif;
      box-shadow:0 16px 40px rgba(0,0,0,0.4);
    `;
    popup.innerHTML = `
      <div style="font-family:'Bricolage Grotesque',sans-serif;
        font-size:17px;font-weight:700;margin-bottom:8px;">${titulo}</div>
      <div style="font-size:14px;color:rgba(255,255,255,0.75);
        line-height:1.5;margin-bottom:16px;">${descricao}</div>
      <div style="display:flex;justify-content:space-between;align-items:center;">
        <span style="font-size:12px;color:rgba(255,255,255,0.4);">
          ${step + 1} de ${steps.length}
        </span>
        <button onclick="window.__mc_onboard_next()" style="
          background:#2DD2E1;color:#0B1133;border:none;
          padding:8px 18px;border-radius:7px;font-weight:600;
          cursor:pointer;font-size:14px;">
          ${step === steps.length - 1 ? 'Concluir' : 'Próximo →'}
        </button>
      </div>
    `;
    document.body.appendChild(popup);

    window.__mc_onboard_next = () => {
      if (alvo) { alvo.style.outline = ''; alvo.style.borderRadius = ''; }
      step++;
      renderStep();
    };
  }

  if (!localStorage.getItem('mc_onboarding_done')) renderStep();
}
```

### 4.6 Verificar perfil e permissão do agente

```js
async function getPerfil() {
  try {
    const r = await fetch('/auth/sign_in', { credentials: 'include' });
    // Alternativa — via Vuex (mais confiável):
    const vue = window.__vue__;
    return vue?.$store?.getters?.getCurrentUser ?? null;
  } catch { return null; }
}

async function isAdmin() {
  const perfil = await getPerfil();
  if (!perfil) return false;
  const conta = (perfil.accounts || []).find(a => a.id === perfil.account_id);
  return conta?.permissions?.includes('administrator') ?? false;
}
```

---

## 5. Seletores CSS importantes do Chatwoot/Kanban

> ⚠ Seletores longos podem quebrar em atualizações do Chatwoot.
> Prefira seletores curtos e semânticos quando possível.

```
.primary-sidebar          sidebar esquerda de ícones
.secondary-sidebar        sidebar de navegação secundária
.conversation-list        lista de conversas
.conversation-item        item individual de conversa
.conversation-details-wrap   painel de conversa aberta
.conversation-footer      área de digitação
.kanban-container         container do quadro Kanban
.kanban-column            coluna do Kanban
.modal-container          qualquer modal
#app                      root da aplicação Vue
```

---

## 6. Variáveis CSS nativas do Chatwoot (sobrescrever com cuidado)

```css
--color-woot-{50..900}    escala da cor primária
--color-primary-medium    HSL da cor primária (usado em shadows e alpha)
--color-background        fundo geral
--color-background-light  fundo alternativo
--color-border            bordas padrão
--color-heading           cor dos títulos
--color-body-text         texto principal
--color-low-risk          verde (status)
--color-medium-risk       amarelo
--color-high-risk         vermelho
```

---

## 7. Roadmap de funcionalidades

### FASE 1 — Branding ✅ CONCLUÍDA
- [x] Paleta de cores Conexão Azul Digital (indigo + cyan)
- [x] Fontes Bricolage Grotesque + Inter + JetBrains Mono
- [x] Logo real (light/dark) com fallback
- [x] Favicon real
- [x] Título "Mágica Chat"
- [x] Sidebar indigo-950 com glow cyan

### FASE 2 — UX Simplificada (próxima)

#### 2A · Command Palette `Ctrl+K`
Digitar e navegar: conversas, contatos, Kanban, configurações.
- Estrutura: overlay escuro + input + lista de resultados
- Fontes de dados: Vuex store (conversas, contatos, rotas)
- Arquivo alvo: `examples/command-palette.html`

#### 2B · Quick Action Bar
Barra flutuante aparece ao abrir uma conversa:
- Resolver com 1 clique
- Transferir para agente/equipe
- Marcar urgente / prioridade
- Adicionar ao Kanban
- Arquivo alvo: `examples/quick-actions.html`

#### 2C · Sidebar Simplificado por Perfil
Mostrar/ocultar itens do menu conforme perfil do agente:
- **Agente:** Conversas, Contatos, Kanban
- **Supervisor:** + Relatórios, Visão Geral
- **Admin:** tudo
- Arquivo alvo: `examples/sidebar-by-role.html`

#### 2D · Card de Conversa Enriquecido
Nos itens da lista de conversas, mostrar:
- Tempo de espera com alerta de cor (verde < 5min, amarelo < 15min, vermelho > 15min)
- Canal de origem (WhatsApp, Instagram, e-mail)
- Agente responsável com avatar
- Arquivo alvo: `examples/rich-conversation-card.html`

### FASE 3 — Funcionalidades Exclusivas

#### 3A · Click-to-Call (FreeSWITCH / Asterisk)
Middleware Node.js + botão no Chatwoot:
- FreeSWITCH via ESL (`modesl`)
- Asterisk via ARI (REST)
- Ramal do agente via `custom_attributes.ramal`
- Arquivo alvo: `examples/call_button.html` (estender) + `server/click-to-call.js`

#### 3B · Timer de SLA
Exibir contador regressivo em cada conversa aberta:
- Calcular com base em `created_at` + SLA configurado
- Mostrar no cabeçalho da conversa
- Pulsar em vermelho ao expirar
- Arquivo alvo: `examples/sla-timer.html`

#### 3C · Painel de Status da Equipe
Flutuante no canto inferior esquerdo:
- Lista de agentes online/offline/ocupado
- Contagem de conversas por agente
- Dado via Vuex store (`agents/getAgents`)
- Arquivo alvo: `examples/team-status.html`

#### 3D · Onboarding para Novos Agentes
Fluxo guiado na primeira vez que o agente acessa:
- Usar padrão da receita 4.5
- Destacar: inbox, criar conversa, Kanban, atalhos
- Controle via `localStorage`
- Arquivo alvo: `examples/onboarding-agent.html`

#### 3E · Exportador de Kanban CSV
Botão no header do Kanban:
- Exportar todos os cards da visualização atual
- Campos: ID, Título, Etapa, Prioridade, Valor, Responsável, Data
- Arquivo alvo: `examples/kanban-export.html`

### FASE 4 — Integrações

#### 4A · Webhook para n8n
Disparar automações n8n em eventos do Chatwoot:
- Conversa resolvida → atualizar CRM
- Card do Kanban movido → notificar equipe
- Arquivo alvo: `examples/n8n-webhooks.html`

#### 4B · Painel Mágica BI
Iframe ou sidebar com dashboard do Mágica BI embutido:
- KPIs em tempo real no Chatwoot
- Arquivo alvo: `examples/magica-bi-panel.html`

#### 4C · Integração Mágica CRM
Mostrar dados do CRM no painel lateral da conversa:
- Histórico de compras, status de pipeline, valor do cliente
- Arquivo alvo: `examples/magica-crm-sidebar.html`

---

## 8. Regras para agentes que desenvolvem neste repo

1. **Branch de trabalho:** sempre `claude/todo-implementation-2pafu3` (ou branch específico da tarefa)
2. **Cada script é autônomo** — não crie dependências entre scripts
3. **MODULE_KEY obrigatório** — use `window['MagicaChat_NomeScript_v{N}']` em todo novo script
4. **MutationObserver obrigatório** — o Vue atualiza o DOM dinamicamente
5. **Testar idempotência** — o script deve funcionar ao ser executado múltiplas vezes
6. **Tokens da marca** — sempre usar as cores/fontes de `colors_and_type.css` (seção 2)
7. **Nome do produto** — sempre "Mágica Chat" (com acento e espaço)
8. **Acessibilidade** — incluir `prefers-reduced-motion` e `focus-visible`
9. **Sem `alert()`** — usar o padrão `toast()` da receita 4.4
10. **Fallback de logo** — sempre incluir `img.onerror` com texto em Bricolage Grotesque
11. **Seletores frágeis** — preferir `.className` curto a seletores aninhados longos
12. **Instalar via:** Super Admin `magicachat.conexaoazul.com/super_admin/dashboard_scripts`

---

## 9. Estrutura do repositório

```
StackLabModulos/
├── CLAUDE.md                          ← este arquivo (leia primeiro)
├── README.md                          ← instruções de instalação
├── dashboard-scripts-documentation.md ← guia técnico de customização
└── examples/
    ├── branding-magicachat.html       ← FASE 1 ✅ branding completo
    ├── call_button.html               ← botão flutuante de chamada
    ├── mobile.html                    ← navbar inferior mobile
    ├── hide-all                       ← ocultar aba "Todos" por perfil
    ├── demo-loading.html              ← referência: tela de loading
    ├── demo-onboarding.html           ← referência: fluxo de onboarding
    └── demo-success-notification.html ← referência: notificação de sucesso
```
