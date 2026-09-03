# Catálogo pessoal de skills

Este repositório mantém um catálogo visual das skills instaladas nos agentes pessoais do usuário. O catálogo serve como um inventário rápido para descobrir o que está disponível, entender para que cada skill serve e conferir em quais agentes e caminhos ela está instalada.

## Como funciona

O arquivo [`skills-catalog.html`](./skills-catalog.html) é uma página autocontida: HTML, CSS e JavaScript ficam no mesmo arquivo. Não há framework, build, servidor local, banco de dados ou dependência externa.

O catálogo é uma fotografia consolidada do ambiente local. A página não acessa o sistema de arquivos para procurar skills enquanto está aberta. Os dados são registrados manualmente no array `skills`, dentro do próprio HTML, depois de verificar as instalações pessoais.

Durante uma atualização, a lógica esperada é:

1. Examinar os diretórios pessoais dos agentes, como `~/.codex/skills`, `~/.claude/skills` e `~/.agents/skills`.
2. Considerar apenas diretórios que contenham um arquivo `SKILL.md`.
3. Excluir diretórios de sistema, runtimes e conteúdos que não sejam skills pessoais.
4. Consolidar instalações repetidas em uma única entrada, usando o slug da skill como identificador.
5. Registrar em `paths` todos os caminhos pessoais onde aquela skill está disponível.
6. Atualizar o array `skills` e conferir os filtros no navegador.

No estado atual, o catálogo registra 53 skills únicas, organizadas em 9 áreas e 3 locais pessoais: Codex, Claude Code e Agents.

## Modelo de cada entrada

Cada objeto do array `skills` representa uma skill:

```js
{
  id: 'slug-da-skill',
  name: 'Nome exibido',
  family: 'Área de uso',
  familyClass: 'classe-visual',
  purpose: 'Descrição curta da finalidade da skill.',
  keywords: 'termos usados na busca',
  locations: ['Codex', 'Claude Code'],
  paths: [
    'C:/Users/Dell/.codex/skills/slug-da-skill',
    'C:/Users/Dell/.claude/skills/slug-da-skill'
  ]
}
```

- `id`: slug estável e único usado para identificar a skill.
- `name`: nome apresentado no card.
- `family`: área exibida no card e usada pelo filtro de área.
- `familyClass`: classe CSS que define a cor visual da área. As classes existentes são `interface`, `code`, `tools`, `discovery` e `motion`.
- `purpose`: explicação, em português, do que a skill faz.
- `keywords`: termos adicionais que tornam a busca mais útil.
- `locations`: agentes que carregam a skill.
- `paths`: caminhos pessoais instalados, exibidos dentro de “ver caminhos instalados”.

## Lógica da página

Quando o HTML carrega, o JavaScript:

1. Lê o array `skills`.
2. Deriva as áreas únicas (`families`) e os locais únicos (`locations`).
3. Preenche os dois `<select>` de filtro.
4. Atualiza os indicadores de total de skills, áreas e locais.
5. Renderiza os cards no elemento `#skillsGrid`.

Cada card mostra o índice original da skill, nome, slug, área, finalidade, agentes disponíveis e os caminhos instalados. Os caminhos ficam recolhidos em um elemento `<details>` para manter a lista principal compacta.

A função `update()` combina os três critérios de filtragem:

- texto digitado: procura em nome, área, finalidade, palavras-chave e agentes;
- área: compara com `skill.family`;
- agente: verifica se o local está em `skill.locations`.

Os critérios são aplicados juntos. O contador de resultados, o texto de estado e a mensagem de lista vazia são atualizados a cada alteração. O botão “Limpar filtros” restaura os três controles e devolve o foco ao campo de busca.

Os valores dinâmicos passam por `escapeHtml()` antes de serem inseridos no HTML renderizado. Isso evita que nomes, descrições, palavras-chave ou caminhos com caracteres especiais sejam interpretados como marcação.

## Como atualizar

Ao instalar uma nova skill:

1. Confirme que existe um `SKILL.md` no diretório da instalação.
2. Verifique se o slug já está no array `skills`.
3. Se já existir, acrescente o caminho e o agente em `paths` e `locations`, sem criar um card duplicado.
4. Se for nova, adicione um objeto com todos os campos do modelo.
5. Use uma `family` existente quando possível; para uma nova área, escolha também uma `familyClass` já suportada pelo CSS.
6. Atualize os valores estáticos de fallback `#totalCount` e `#resultCount` no HTML. Com JavaScript habilitado eles são recalculados automaticamente, mas continuam úteis caso o script não seja executado.
7. Abra o arquivo em um navegador e teste busca, filtro por área, filtro por agente, limpeza dos filtros e expansão dos caminhos.

## Organização das instalações

As skills podem ser mantidas em um diretório canônico compartilhado e expostas aos agentes por junctions ou links simbólicos. O catálogo registra os caminhos efetivamente disponíveis para cada agente, mas não cria, atualiza ou remove essas instalações.

Isso separa responsabilidades:

- o gerenciador de skills cuida dos arquivos e links;
- o catálogo documenta o estado conhecido dessas instalações;
- o navegador apenas apresenta e filtra os dados registrados.

## Executar

Não é necessário instalar nada. Basta abrir [`skills-catalog.html`](./skills-catalog.html) diretamente no navegador. Para publicar, qualquer hospedagem de arquivos estáticos é suficiente.

## Limitações

- O catálogo não é sincronizado automaticamente com `~/.codex`, `~/.claude` ou `~/.agents`.
- Alterações futuras nas instalações precisam ser refletidas manualmente no array `skills`.
- Os caminhos registrados são específicos do ambiente Windows atual.
- O catálogo descreve a finalidade das skills; ele não substitui o conteúdo original de cada `SKILL.md`.
