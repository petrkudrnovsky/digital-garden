PHP v Ubuntu mám uložené v `/etc/php/`.

```sh
sudo apt update && sudo apt upgrade # aktualizace balicku
sudo apt install software-properties-common # pro manage nezavislych zdroju SW
sudo add-apt-repository ppa:ondrej/php # ma ruzne verze PHP pro Ubuntu
sudo apt update # includnou se nove balicky
sudo apt install php8.1 # nebo jakkoukoliv jinou verzi
```