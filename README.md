# tg_tunnel

Обратный SOCKS-туннель для MTProto-прокси (mtg), у которого нет своего выхода к Telegram.
Скрипт крутится на машине с выходом в интернет и держит `ssh -R 127.0.0.1:1080 <хост прокси>`:
на хосте прокси порт 127.0.0.1:1080 становится SOCKS-выходом через эту машину, mtg ходит в Telegram через него.

- `tg-tunnel` — цикл ssh с перезапуском через 5 с; `flock` не даёт поднять второй экземпляр.
- `crontab` — запуск при загрузке и раз в минуту (живой экземпляр держит блокировку, лишние выходят сразу).

## Установка

На машине с выходом:

```sh
install -D -m 755 tg-tunnel ~/.local/bin/tg-tunnel
mkdir -p ~/.local/state
crontab -l | cat - crontab | crontab -
```

В `~/.ssh/config` — алиас `testsrv` на хост прокси (адрес, пользователь, ключ).

На хосте прокси ключ туннеля ограничен только пробросом порта, в `authorized_keys`:

```
restrict,port-forwarding,permitlisten="127.0.0.1:1080",command="/bin/false" ssh-ed25519 AAAA… tgproxy-tunnel
```

Журнал — `~/.local/state/tg-tunnel.log`. Проверка работы прокси — отдельный проект tg_proxy_check.
