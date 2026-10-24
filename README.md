# ♟ MyChessBot

Шахматный бот для [Lichess](https://lichess.org), который играет под отдельным **BOT-аккаунтом** через официальный [Lichess Bot API](https://lichess.org/api#tag/Bot). В качестве движка используется Stockfish (любой UCI-движок можно заменить в настройках).

> ⚠️ Бот работает **только** с аккаунтом, зарегистрированным как BOT. Использовать его на обычном аккаунте или подсказывать ходы в играх с людьми запрещено правилами Lichess и приведёт к блокировке.

## Возможности

- Принимает вызовы и играет партии автоматически
- Поддержка контролей времени: блиц, рапид, классика, коррспонденция
- Любой UCI-движок (по умолчанию Stockfish)
- Настраиваемые фильтры вызовов: рейтинг, контроль времени, рейтинговые/товарищеские партии
- Дебютная книга (опционально)
- Предложение ничьей и сдача по настраиваемым условиям
- Логирование партий и ошибок

## Требования

- Python 3.9+
- Аккаунт Lichess, конвертированный в BOT
- Бинарный файл Stockfish ([скачать](https://stockfishchess.org/download/))

## Установка

```bash
git clone https://github.com/<your-name>/MyChessBot.git
cd MyChessBot
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

Положите Stockfish в папку `engines/` и убедитесь, что файл исполняемый:

```bash
chmod +x engines/stockfish
```

## Получение токена и создание BOT-аккаунта

1. Создайте **новый** аккаунт на Lichess. Он не должен был сыграть ни одной партии, иначе конвертация невозможна.
2. Откройте [Personal API tokens](https://lichess.org/account/oauth/token/create) и создайте токен с правом **Play games with the bot API** (`bot:play`).
3. Выполните конвертацию в BOT-аккаунт. **Это необратимо.**

```bash
python lichess-bot.py -u
```

## Настройка

Скопируйте пример конфига и отредактируйте его:

```bash
cp config.yml.default config.yml
```

Основные параметры:

```yaml
token: "ВАШ_ТОКЕН"
url: "https://lichess.org/"

engine:
  dir: "./engines/"
  name: "stockfish"
  protocol: "uci"
  uci_options:
    Threads: 2
    Hash: 256

challenge:
  concurrency: 1
  accept_bot: true
  variants:
    - standard
  time_controls:
    - bullet
    - blitz
    - rapid
  modes:
    - casual
    - rated
```

> 🔒 Никогда не публикуйте `config.yml` с токеном. Добавьте его в `.gitignore`.

## Запуск

```bash
python lichess-bot.py
```

Если всё настроено верно, в консоли появится сообщение о подключении, а профиль бота на Lichess получит значок **BOT**. Чтобы сыграть с ним, отправьте вызов со своего личного аккаунта.

### Запуск в Docker

```bash
docker build -t mychessbot .
docker run -d --name mychessbot -v $(pwd)/config.yml:/app/config.yml mychessbot
```

## Структура проекта

```
MyChessBot/
├── engines/            # движки (Stockfish и др.)
├── books/              # дебютные книги (.bin)
├── lichess-bot.py      # точка входа
├── config.yml.default  # пример конфигурации
├── requirements.txt
└── README.md
```

## Частые проблемы

| Проблема | Решение |
|---|---|
| `401 Unauthorized` | Токен неверный или без права `bot:play` |
| `This API endpoint can only be used with a Bot account` | Аккаунт не конвертирован в BOT |
| Бот не принимает вызовы | Проверьте раздел `challenge` в `config.yml` |
| Движок не запускается | Проверьте путь, имя файла и права на выполнение |
| Бот теряет время | Уменьшите `Hash`/`Threads` или увеличьте `move_overhead` |

## Fair play

Бот должен быть честно обозначен как BOT, а им управляет программа, а не человек. Соблюдайте [Условия использования Lichess](https://lichess.org/terms-of-service) и не запускайте бота на личном аккаунте.

## Вклад в проект

Pull request'ы приветствуются. Для крупных изменений сначала откройте issue.

## Лицензия

Распространяется по лицензии [MIT](LICENSE).

## Благодарности

- [Lichess](https://lichess.org) за открытое API
- [Stockfish](https://stockfishchess.org) за движок
- [lichess-bot](https://github.com/lichess-bot-devs/lichess-bot) за референсную реализацию
