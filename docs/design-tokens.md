# 🎨 Tokens de Design

**Projeto:** MuletAí
**Versão:** 1.0.0
**Última atualização:** 2026-09-22

> 🤖 **Este documento existe para a IA parar de inventar um botão diferente a cada
> tela.** Não é um design system — é o mínimo que dá à prototipagem assistida algo a
> que obedecer.
>
> ✍️ **Não preencha na mão:** rode `/utf-design` (depois do `/utf-flows`).

---

## Paleta

Nome semântico, nunca `azul-2` — a cor muda, o papel dela não.

| Token | Valor | Onde se usa |
| --- | --- | --- |
| `primaria` | `#1F6C75` (G88) | ação principal |
| `superficie` | `#F5F9F9` (G4) | fundo de card e painel |
| `texto` | `#015D67` (Forest Green) | texto padrão |
| `texto-suave` | `#47878E` (G72) | legenda, apoio |
| `perigo` | `#E57373` | erro, exclusão |
| `sucesso` | `#CAF0C1` (Pistachio) | confirmação |
| `desabilitado` | `#99BDC1` (G40) | controle inativo |

> A paleta parte de um tema "verde hospitalar" (Forest Green / Kelly Green / Mint /
> Pistachio). `primaria` e `texto` usam tons próximos de propósito (dois teals
> escuros) — decisão consciente do aluno, não confusão de nomenclatura.

## Escala de espaçamento

Uma progressão só, usada em tudo.

| Token | Valor |
| --- | --- |
| `xs` | 4px |
| `sm` | 8px |
| `md` | 16px |
| `lg` | 24px |
| `xl` | 32px |

## Tipografia

| Token | Família · tamanho · peso | Papel |
| --- | --- | --- |
| `titulo-pagina` | Inter · 28px · 700 | título de página |
| `titulo-card` | Inter · 20px · 600 | título de card |
| `corpo` | Merriweather · 16px · 400 | corpo (texto longo) |
| `legenda` | Inter · 14px · 400 | legenda, apoio |

## Estados de botão

| Estado | Aparência |
| --- | --- |
| normal | fundo `primaria`, texto branco |
| hover | fundo `primaria` ~10% mais escuro, texto branco |
| foco (teclado) | fundo `primaria`, contorno de 2px na cor `sucesso` (contraste ~4,8:1) |
| desabilitado | fundo `desabilitado`, texto na cor `texto` (contraste ~3,8:1) |
| carregando | fundo `primaria`, indicador de progresso no lugar do texto, bloqueado para novos cliques |

## Protótipo

**Link:** *(pendente — o aluno ainda não montou um protótipo)*
**Telas:** *(pendente — quando o protótipo existir, deve cobrir as telas da Jornada 1: painel, formulário de contribuição, tela de processamento e confirmação)*
