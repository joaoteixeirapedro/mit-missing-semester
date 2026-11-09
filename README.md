# MIT — The Missing Semester of Your CS Education 2026

This repository documents my progress through MIT's
The Missing Semester of Your CS Education.

## Goals

- Become comfortable using the command line
- Improve my Git and GitHub workflow
- Learn shell scripting
- Learn tools such as grep, sed, awk, jq and tmux
- Understand debugging, profiling and automation
- Keep a public record of my exercises and projects

## Progress

- [x] Lecture 1 — Course Overview + Introduction to the Shell
- [x] Lecture 2 — Command-line Environment
- [x] Lecture 3 — Development Environment and Tools
- [x] Lecture 4 — Debugging and Profiling
- [ ] Lecture 5 — Version Control and Git
- [ ] Lecture 6 — Packaging and Shipping Code
- [ ] Lecture 7 — Agentic Coding
- [ ] Lecture 8 — Beyond the Code
- [ ] Lecture 9 — Code Quality


## Contexto e conteúdo

Este repositório reúne minhas respostas e anotações do curso [The Missing Semester of Your CS Education](https://missing.csail.mit.edu/). O objetivo é praticar ferramentas de linha de comando que complementam a formação em Engenharia de Software.

| Aula | Evidência do estudo |
| --- | --- |
| [Shell](lecture-01-shell/exercises.md) | Navegação, redirecionamento, permissões e pipelines |
| [Ambiente de linha de comando](lecture-02-command-line-environment/exercises.md) | Configuração e uso do terminal |
| [Ferramentas de desenvolvimento](lecture-03-development-environment-and-tools/exercises.md) | Ferramentas para organizar o fluxo de trabalho |
| [Depuração e profiling](lecture-04-debugging-and-profiling/exercises.md) | Técnicas para investigar comportamento e desempenho |

## Como consultar e reproduzir

Requisitos: Git e um terminal Bash em Linux, macOS ou WSL. Os arquivos `exercises.md` são anotações, então não há dependências para instalar nem servidor para iniciar.

```bash
git clone https://github.com/joaoteixeirapedro/mit-missing-semester.git
cd mit-missing-semester
cat lecture-01-shell/exercises.md
```

Os blocos de comando nos exercícios podem ser reproduzidos individualmente em um diretório de teste. Alguns exemplos pressupõem ferramentas como `jq`, `awk` ou acesso à rede; confira o contexto antes de executar.

## Demonstração

Um dos exercícios mostra como combinar comandos para contar extensões de arquivos:

```bash
find . -type f -printf '%f\n' | sed -n 's/.*\.//p' | sort | uniq -c | sort -nr | head -5
```

Veja a explicação e outros exemplos em [Aula 1 — Shell](lecture-01-shell/exercises.md).

## Licença e atribuição

Minhas anotações e soluções autorais estão sob [licença MIT](LICENSE). O material original do curso pertence aos respectivos autores do MIT e está disponível no [site oficial](https://missing.csail.mit.edu/). Arquivos locais de editor e ambiente são excluídos pelo [`.gitignore`](.gitignore).
