# Mágica Chat — Checklist de Testes

Use este checklist após instalar os módulos em `magicachat.conexaoazul.com`.  
Marque cada item conforme validado. Qualquer falha: descreva o comportamento e reporte.

---

## MagicaChat-Branding

- [ ] Sidebar esquerda com fundo escuro `#0B1133` (indigo profundo)
- [ ] Ícone ativo na sidebar destacado em cyan `#2DD2E1`
- [ ] Botões primários em cyan com texto indigo escuro
- [ ] Headings e títulos em **Bricolage Grotesque** (fonte display grossa)
- [ ] Corpo e menus em **Inter**
- [ ] Título da aba do browser: "Mágica Chat" (não "Chatwoot")
- [ ] Logo substituído pelo logo da Conexão Azul Digital
- [ ] Favicon atualizado (logo mark da marca)
- [ ] Scrollbar discreta em cyan na lista de conversas
- [ ] Colunas do Kanban com borda cyan no topo
- [ ] Animações respeitam `prefers-reduced-motion` (testar em sistema com opção ativada)

---

## MagicaChat-SidebarByRole

**Como agente:**
- [ ] Links de Relatórios somem da sidebar
- [ ] Link de Configurações some da sidebar
- [ ] Sem espaço vazio no lugar dos itens ocultos
- [ ] Sem flash de itens proibidos ao carregar a página

**Como supervisor:**
- [ ] Relatórios visível na sidebar
- [ ] Configurações oculto na sidebar

**Como admin:**
- [ ] Todos os itens visíveis (nenhum oculto)

**Geral:**
- [ ] Após navegar entre páginas (Vue Router), os itens continuam ocultos corretamente

---

## MagicaChat-OcultarAbaTodos

- [ ] Logado como **agente**: aba "Todos" / "All Contacts" some da lista de contatos
- [ ] Logado como **admin**: aba "Todos" permanece visível
- [ ] Após reload da página, a ocultação se mantém

---

## MagicaChat-CallButton

- [ ] Botão flutuante verde aparece no canto inferior direito
- [ ] Clicar no botão abre o modal de chamada
- [ ] Campo vazio ou número inválido (< 8 dígitos): borda vermelha + mensagem de erro
- [ ] Mensagem de erro desaparece ao começar a digitar
- [ ] Número válido → clicar "Ligar" → modal fecha, toast de confirmação aparece
- [ ] Toast desaparece automaticamente após ~4 segundos
- [ ] Campo de telefone limpo após ligar
- [ ] `Escape` fecha o modal
- [ ] Clicar fora do modal fecha

---

## MagicaChat-Mobile

> Testar em dispositivo móvel ou com DevTools em modo responsivo (< 768px)

- [ ] Navbar inferior com 5 ícones aparece no mobile
- [ ] Sidebar desktop some no mobile
- [ ] Conteúdo principal não fica sobreposto pela navbar
- [ ] Navegação pelos ícones funciona (Conversas, Contatos, Kanban, Relatórios, Ajustes)
- [ ] Em desktop (> 768px): navbar inferior não aparece

---

## MagicaChat-Onboarding

**Primeira vez:**
- [ ] Ao logar com um agente novo: tour inicia automaticamente
- [ ] Spotlight cyan destaca corretamente cada elemento nos steps
- [ ] Popover posicionado sem sair da tela
- [ ] `→` ou botão "Próximo" avança o step
- [ ] `←` ou botão "Anterior" volta o step
- [ ] `Esc` pula todo o tour
- [ ] Último step: botão "Concluir" aparece
- [ ] Após concluir: nenhuma sobreposição ou elemento residual

**Segunda vez (mesmo agente):**
- [ ] Reload da página → tour **não** reaparece
- [ ] Nova aba / nova sessão → tour **não** reaparece

**Perfis:**
- [ ] Como agente: steps de Relatórios e Configurações não aparecem
- [ ] Como supervisor: step de Relatórios aparece, Configurações não
- [ ] Como admin: todos os steps aparecem

---

## MagicaChat-CommandPalette

- [ ] `Ctrl+K` (Windows/Linux) ou `Cmd+K` (Mac) abre a paleta
- [ ] Overlay escuro com blur aparece
- [ ] Input em foco automaticamente ao abrir
- [ ] Digitar filtra resultados em tempo real (case-insensitive)
- [ ] Resultados mostram: rotas, conversas abertas, contatos
- [ ] `↑` / `↓` navega entre itens (item ativo com borda cyan)
- [ ] `Enter` abre o item selecionado
- [ ] `Esc` fecha a paleta
- [ ] Clicar fora da paleta fecha
- [ ] `Ctrl+K` novamente fecha a paleta se estiver aberta
- [ ] Campo limpo ao reabrir

**Perfis:**
- [ ] Como agente: Relatórios e Configurações **não** aparecem nas rotas
- [ ] Como supervisor: Relatórios aparece, Configurações não
- [ ] Como admin: todas as rotas aparecem

**Sem resultados:**
- [ ] Digitar algo inexistente exibe "Nenhum resultado"

---

## MagicaChat-QuickActions

**Aparência:**
- [ ] Abrir uma conversa → barra flutuante centralizada aparece na parte inferior
- [ ] Barra tem fundo indigo escuro com borda cyan sutil
- [ ] 4 botões: Resolver (cyan), Transferir, Urgente, Kanban
- [ ] Fechar a conversa (voltar à lista) → barra some

**Resolver:**
- [ ] Clicar "Resolver" → toast verde "Conversa resolvida com sucesso!" aparece
- [ ] Status da conversa muda para resolvida no Chatwoot

**Transferir:**
- [ ] Clicar "Transferir" → popover com lista de agentes abre para cima
- [ ] Lista mostra avatar (ou inicial) + nome do agente
- [ ] Selecionar agente → popover fecha, toast de confirmação aparece
- [ ] `Esc` fecha o popover sem transferir
- [ ] Clicar fora do popover fecha

**Urgente:**
- [ ] Clicar "Urgente" → botão fica vermelho
- [ ] Clicar novamente → botão volta ao estado neutro
- [ ] Toast confirma cada ação

**Kanban:**
- [ ] Clicar "Kanban" → navega para o quadro Kanban da conta

**Navegação:**
- [ ] Navegar entre conversas diferentes → barra atualiza (prioridade re-avaliada)
- [ ] Navegar para página sem conversa (ex: Contatos) → barra some
- [ ] Voltar para conversa → barra reaparece

---

## Geral — todos os módulos

- [ ] Nenhum erro de JavaScript no console (F12 → Console)
- [ ] Nenhum módulo executa mais de uma vez (verificar via `console.log` de inicialização)
- [ ] Ao desabilitar um script no Super Admin, o comportamento some após reload
- [ ] Scripts funcionam simultaneamente sem conflito entre si
