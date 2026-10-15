# 🎬 Radar de Lançamentos

Automação em Python que acompanha **filmes e séries** pela API do TMDB e avisa quando:

- 📅 a data de estreia é definida ou muda
- 🎞️ um novo trailer é publicado
- 📺 um novo episódio é agendado ou lançado
- 🍿 o filme chega aos cinemas

Os avisos aparecem no terminal, e as datas viram eventos no **Google Calendar**
(ou arquivos `.ics`, caso você não configure o Google).

## Desenvolvedores 

- Debora da Silva Amaral - RM 550412 
- Eduardo Pielich - RM 99767 
- Livia Namba Seraphim - RM 97819 

## Como funciona

```
watchlist (SQLite) ──► TMDB API ──► compara com o último snapshot ──► avisos + calendário
```

1. `add` busca o título e salva um *snapshot* (status, datas, episódios, trailers).
2. `check` consulta o TMDB de novo e compara com o snapshot salvo (`radar/checker.py`).
3. Cada diferença relevante vira uma notificação; as datas futuras viram eventos de dia inteiro.
   Os eventos têm ID determinístico, então rodar várias vezes **atualiza** em vez de duplicar.

## Instalação

```bash
python -m venv .venv && source .venv/bin/activate  
pip install -r requirements.txt
cp .env.example .env                              
```

- **TMDB (obrigatório):** chave gratuita em https://www.themoviedb.org/settings/api
- **Google Calendar (opcional):** crie credenciais OAuth "Desktop app" no Google Cloud Console,
  ative a Calendar API e salve o arquivo como `credentials.json`. No primeiro uso o navegador
  abre para você autorizar. Sem isso, o radar gera arquivos `.ics` em `calendario/`.

## Uso

```bash
python main.py add "Duna" --tipo filme
python main.py add "Severance" --tipo serie
python main.py list
python main.py check            
python main.py watch --horas 6 
python main.py remove 2
```

Dica: agende `python main.py check` no cron / Agendador de Tarefas do Windows.

## Testes

```bash
python -m unittest discover tests
```

## Estrutura

```
main.py                CLI (argparse + rich)
radar/tmdb.py          cliente REST do TMDB (requests, retry automático)
radar/db.py            SQLite
radar/checker.py       detecção de mudanças
radar/notifier.py      terminal + Telegram
radar/calendar_sync.py Google Calendar API / .ics
tests/                 testes da lógica de diff
```
