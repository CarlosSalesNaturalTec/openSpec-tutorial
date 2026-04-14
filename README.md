# OpenSpec — Referência de Workflows

## NÃO CONHEÇO O CONTEXTO / REQUISITOS AMBÍGUOS
```
/opsx:explore     → investigar, analisar, comparar opções 
  └─► /opsx:new {nome-slug-da-mudança}
        ├─► /opsx:continue  → cria um artefato por vez    
        OU
        └─► /opsx:ff        → cria todos de uma vez

        Após qualquer um dos dois acima, siga com:
        
        /opsx:apply          → implementa as tasks
        /opsx:sync           → (opcional) sincroniza specs sem fechar. Recomendado em mudanças longas ou complexas
        /opsx:verify         → (opcional) valida specs vs código. Recomendado em mudanças longas ou complexas
        /opsx:archive        → finaliza e arquiva a mudança
```


## JÁ SEI O QUE FAZER — perfil core
```
/opsx:propose {nome-slug-da-mudança} → cria todos os artefatos de uma vez (o equivalente a: new + ff)
  └─► /opsx:apply 
  └─► /opsx:archive
```


## JÁ SEI O QUE FAZER — perfil expandido 
```
/opsx:new {nome-slug-da-mudança}
  ├─► /opsx:continue  → cria um artefato por vez    
  OU
  └─► /opsx:ff        → cria todos de uma vez

  Após qualquer um dos dois acima, siga com:
        
  /opsx:apply          → implementa as tasks
  /opsx:sync           → (opcional) sincroniza specs sem fechar. Recomendado em mudanças longas ou complexas
  /opsx:verify         → (opcional) valida specs vs código. Recomendado em mudanças longas ou complexas
  /opsx:archive        → finaliza e arquiva a mudança
```


## UTILITÁRIOS
```
/opsx:bulk-archive  → arquiva múltiplas mudanças de uma vez
/opsx:onboard       → tutorial guiado para novos usuários (~15 min)
```


## OBSERVAÇÕES GERAIS:

* `{nome-slug-da-mudança}` = kebab-case, curto, mas específico o suficiente para entender o que é a mudança sem precisar abrir a pasta. Ex: add-dark-mode, fix-login-redirect, implement-2fa

### Artefatos:
```
proposal.md   → por quê estamos fazendo isso, o que muda
specs/        → requisitos e cenários (delta specs)
design.md     → abordagem técnica
tasks.md      → checklist de implementação
```

* A ordem importa porque há dependências entre eles: proposal  →  specs  →  design  →  tasks (tasks é bloqueado até specs existir)

* A edição manual dos artefatos proposal, design e tasks é totalmente suportada. O OPSX lê os artefatos do filesystem em tempo real. Na próxima vez que chamar `/opsx:apply`, `/opsx:verify` ou `/opsx:sync`, ele já considera o conteúdo atualizado. Inclusive `tasks.md` marcando ou desmarcando checkboxes, `/opsx:apply` lê o tasks.md e trabalha apenas nas tasks que ainda estão [ ] desmarcadas;

* Atenção, se ditou os **delta specs**, rode `/opsx:sync` logo depois. Isso garante que as mudanças nas delta specs sejam propagadas para `openspec/specs/` (source of truth).

### Specs: 
* Analogia com Git: 
  * `openspec/specs/` →  é como o branch main (estado atual) 
  * `openspec/changes/archive` →  é como o histórico de commits (o que cada mudança fez)

* Alterações em `openspec/specs/`:
  * Quando faz sentido: Correção de typo, erro gramatical, formatação. Clareza de linguagem sem mudar o significado. Algo pequeno que não vale abrir uma mudança formal.
  * **Não** altere manualmente, use o workflow (`propose`, `new`, etc) em qualquer situação que mude comportamento real, exemplo:  Adicionar requisito novo, Modificar como o sistema deve se comportar, Remover uma regra de negócio, Renomear um requisito.
  * Resumindo com uma regra simples: 
  1. Muda COMO o texto está escrito?  →  edita manual, sem problema
  2. Muda O QUE o sistema deve fazer? →  abre uma mudança via workflow


### Atualizar mudança atual vs criar nova:
- Mesmo escopo (objetivo continua igual) → edite os artefatos da mudança atual
- Escopo diferente (novo objetivo) → `/opsx:new {outro-slug}`

Regra simples: se a mudança tivesse que ser revertida, faria sentido reverter as duas coisas juntas? 
- Sim → mesma mudança
- Não → nova mudança

### Estrutura de pastas default
```
openspec/
├── specs/              → source of truth (estado atual do sistema)
└── changes/
    ├── add-dark-mode/  → mudança ativa
    │   ├── proposal.md
    │   ├── design.md
    │   ├── tasks.md
    │   └── specs/      → delta specs (só o que muda)
    └── archive/        → histórico de mudanças concluídas
```