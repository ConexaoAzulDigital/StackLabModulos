# Mágica Chat — Dashboard Scripts

> **Produto:** Mágica Chat (Chatwoot + Kanban para Chatwoot)  
> **Empresa:** Conexão Azul Digital — [conexaoazul.com](https://www.conexaoazul.com)  
> **Instância:** [magicachat.conexaoazul.com](https://magicachat.conexaoazul.com)

Módulos prontos para instalação como **Dashboard Scripts** no Mágica Chat.  
Cada módulo é um bloco `<script>` autônomo que personaliza aparência e comportamento sem tocar no código-fonte do Chatwoot.

---

## Módulos disponíveis

| Script | Arquivo | Fase | Descrição |
|---|---|---|---|
| `MagicaChat-Branding` | `examples/branding-magicachat.html` | 1 ✅ | Paleta, fontes, logo e título da marca |
| `MagicaChat-OcultarAbaTodos` | `examples/hide-all` | 1 ✅ | Oculta aba "Todos" para não-admins |
| `MagicaChat-CallButton` | `examples/call_button.html` | 1 ✅ | Botão flutuante de chamada telefônica |
| `MagicaChat-Mobile` | `examples/mobile.html` | 1 ✅ | Navbar inferior para dispositivos móveis |
| `MagicaChat-Onboarding` | `examples/onboarding-agent.html` | 3D ✅ | Tour guiado na primeira sessão do agente |
| `MagicaChat-CommandPalette` | `examples/command-palette.html` | 2A ✅ | Paleta de busca global (`Ctrl+K`) |
| `MagicaChat-QuickActions` | `examples/quick-actions.html` | 2B ✅ | Barra de ações rápidas por conversa |
| `MagicaChat-SidebarByRole` | `examples/sidebar-by-role.html` | 2C ✅ | Menu filtrado por perfil do agente |

---

## Como instalar

1. Acesse: `magicachat.conexaoazul.com/super_admin/dashboard_scripts`
2. Clique em **New Script**
3. Dê o nome exato da coluna "Script" da tabela acima
4. Cole **apenas o bloco `<script>…</script>`** do arquivo correspondente (sem as tags HTML ao redor)
5. Habilite e salve

### Ordem recomendada de instalação

```
1. MagicaChat-Branding          ← fontes e cores base (outros dependem disto)
2. MagicaChat-SidebarByRole     ← filtragem de menu (sem flash visual)
3. MagicaChat-OcultarAbaTodos   ← ocultar aba Todos para agentes
4. MagicaChat-Onboarding        ← tour de boas-vindas
5. MagicaChat-CommandPalette    ← Ctrl+K
6. MagicaChat-QuickActions      ← barra de ações por conversa
7. MagicaChat-CallButton        ← botão de chamada (opcional)
8. MagicaChat-Mobile            ← navbar mobile (opcional)
```

---

## O que cada módulo faz

### MagicaChat-Branding
- Paleta de cores real da Conexão Azul Digital (indigo `#1E2D78` + cyan `#2DD2E1`)
- Fontes: Bricolage Grotesque (headings), Inter (body), JetBrains Mono (código)
- Logo `logo-light/dark.svg` com fallback em texto
- Favicon e título da aba: "Chatwoot" → "Mágica Chat"
- Sidebar indigo-950, botões cyan, scrollbar discreta
- Respeita `prefers-reduced-motion`

### MagicaChat-SidebarByRole
- **Agente:** Conversas, Contatos, Kanban
- **Supervisor:** + Relatórios, Visão Geral
- **Admin:** tudo visível
- CSS fallback instantâneo (sem flash de itens proibidos)
- Observer throttled 300ms para re-aplicar após navegação Vue

### MagicaChat-OcultarAbaTodos
- Busca perfil via `/api/v1/profile`
- Agentes não veem a aba "Todos" / "All Contacts"
- Admins mantêm acesso

### MagicaChat-CallButton
- Botão flutuante para iniciar ligações
- Validação de telefone (8–15 dígitos) com erro inline
- Toast de confirmação animado
- Fechar com `Escape` ou clique fora
- Extensível para FreeSWITCH/Asterisk via `fetch()` ao middleware

### MagicaChat-Mobile
- Navbar inferior para telas < 768px com ícones Remix Icons
- Oculta sidebar desktop no mobile
- Dark theme forçado no mobile

### MagicaChat-Onboarding
- Tour guiado somente na **primeira sessão** do agente
- Spotlight real nos elementos do Chatwoot/Kanban
- 8 steps mapeados ao workflow real do Mágica Chat
- Steps filtrados por perfil (agente / supervisor / admin) via Vuex
- Conclusão salva em `localStorage` por `user.id`
- Teclado: `→` próximo, `←` anterior, `Esc` pular

### MagicaChat-CommandPalette
- Abre com `Ctrl+K` (ou `Cmd+K` no Mac)
- Busca em tempo real: rotas fixas, conversas abertas e contatos (via Vuex)
- Navegação por teclado: `↑↓` navegar, `↵` abrir, `Esc` fechar
- Rotas filtradas por perfil do agente

### MagicaChat-QuickActions
- Barra flutuante centralizada ao abrir qualquer conversa
- **Resolver** — resolve via API com toast de confirmação
- **Transferir** — popover com lista de agentes para reatribuição
- **Urgente** — toggle de prioridade `high` / `null`
- **Kanban** — navega para o quadro Kanban
- Usa `window.axios` global disponível no Chatwoot

---

## Checklist de testes

Ver [TESTING.md](./TESTING.md) para o checklist completo de validação de cada módulo.

---

## Desenvolver novos módulos

Leia [CLAUDE.md](./CLAUDE.md) — guia completo para agentes com:
- Padrão canônico de script (MODULE_KEY + MutationObserver)
- Tokens de marca (cores, fontes, logos)
- Receitas prontas (botão flutuante, toast, onboarding, sidebar item)
- Seletores CSS do Chatwoot/Kanban
- Roadmap de funcionalidades

### Padrão mínimo

```html
<script data-name="MagicaChat-MeuScript">
(function () {
  const KEY = 'MagicaChat_MeuScript_v1';
  if (window[KEY]) return;
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

## Suporte

Para dúvidas e integrações, entre em contato pela comunidade da Conexão Azul Digital.
