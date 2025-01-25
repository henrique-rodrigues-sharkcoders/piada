import random
import speech_recognition as sr
import pyttsx3
import webbrowser
import tkinter as tk
from tkinter import messagebox
import urllib.parse
import requests

# Lista de piadas dentro do próprio código
jokes_list = [
    "Why don't skeletons fight each other? Because they don't have the guts!",
    "What do you call fake spaghetti? An impasta!",
    "Why did the scarecrow win an award? Because he was outstanding in his field!",
    "Why don’t oysters donate to charity? Because they are shellfish!",
    "What did the ocean say to the beach? Nothing, it just waved!",
    "Why can’t you trust an atom? Because they make up everything!",
    "Why did the bicycle fall over? Because it was two-tired!",
    "What do you get if you cross a snowman and a vampire? Frostbite!",
    "Why don't eggs tell jokes? Because they'd crack each other up!",
    "How does a penguin build its house? Igloos it together!",
    "Why was the math book sad? Because it had too many problems!",
    "What do you call a bear with no teeth? A gummy bear!",
    "What do you call cheese that isn't yours? Nacho cheese!",
    "What did the grape do when it got stepped on? Nothing but let out a little wine!",
    "Why was the computer cold? Because it left its Windows open!",
    "How do you organize a space party? You planet!",
    "Why don’t some couples go to the gym? Because some relationships don’t work out!",
    "Why did the tomato turn red? Because it saw the salad dressing!",
    "What do you call a sleeping bull? A bulldozer!",
    "Why did the golfer bring two pairs of pants? In case he got a hole in one!",
    "Why did the chicken join a band? Because it had the drumsticks!",
    "What do you call a fish with no eyes? A fsh!",
    "What do you get when you cross a snowman and a dog? Frostbite!",
    "Why can’t you give Elsa a balloon? Because she’ll let it go!",
    "Why was the broom late? It swept in!",
    "What did the left eye say to the right eye? Between you and me, something smells!",
    "What did one wall say to the other wall? I'll meet you at the corner!",
    "What did one plate say to the other plate? Lunch is on me!",
    "Why did the coffee file a police report? It got mugged!",
    "Why don’t skeletons fight each other? They don’t have the stomach for it!",
    "Why don’t vampires use Facebook? Because they can't handle the 'like' button!",
    "What’s orange and sounds like a parrot? A carrot!",
    "Why don’t ants get sick? Because they have tiny ant-bodies!",
    "What’s a skeleton’s least favorite room? The living room!",
    "What did the ocean say to the beach? Nothing, it just waved!",
    "What do you call an alligator in a vest? An investigator!",
    "Why don’t programmers like nature? It has too many bugs!",
    "How do you make holy water? You boil the hell out of it!",
    "Why did the banana go to the doctor? Because it wasn’t peeling well!"
]

# Inicializa o motor de texto-para-fala
engine = pyttsx3.init()


# Função para falar o texto
def speak(text):
    engine.say(text)
    engine.runAndWait()


# Função para ouvir comandos de voz
def listen():
    r = sr.Recognizer()
    with sr.Microphone() as source:
        print("Listening for your command...")
        audio = r.listen(source)

        try:
            command = r.recognize_google(audio, language="en-US")  # Reconhecimento de fala em inglês
            print(f"You said: {command}")
            return command
        except sr.UnknownValueError:
            print("Sorry, I couldn't understand that.")
            return None
        except sr.RequestError:
            print("There was an error with the speech recognition service.")
            return None


# Função para tocar música automaticamente no YouTube
def play_music(query):
    # Codifica a query para garantir que os espaços sejam convertidos corretamente para a URL
    search_query = urllib.parse.quote_plus(query)

    # Faz uma requisição simples ao YouTube para obter o primeiro vídeo da pesquisa
    youtube_search_url = f"https://www.youtube.com/results?search_query={search_query}"

    # Abre a URL de pesquisa
    webbrowser.open(youtube_search_url)

    speak(f"Playing {query} on YouTube. Enjoy the music!")


# Função para o botão do microfone
def on_mic_button_click():
    mic_button.config(state="disabled")  # Desabilitar o botão para evitar múltiplos cliques
    status_label.config(text="Listening...")
    response = listen()

    if response:
        response = response.lower()  # Converte a resposta para minúsculas

        if "exit" in response:
            speak("Goodbye!")
            root.quit()  # Fecha a aplicação
        elif "how are you" in response:
            speak("I'm doing well, thank you for asking!")
            status_label.config(text="I'm doing well!")
        elif "google" in response:
            speak("Opening Google now.")
            webbrowser.open("https://www.google.com")
            status_label.config(text="Opening Google...")
        elif "tell me a joke" in response:
            joke = random.choice(jokes_list)
            speak(f"Here's a joke: {joke}")
            status_label.config(text="Here's a joke!")
        elif "search" in response:
            search_query = response.replace("search", "").strip()  # Remove a palavra "search" e limpa os espaços
            if search_query:
                speak(f"Searching for {search_query} on Google.")
                webbrowser.open(f"https://www.google.com/search?q={search_query}")
                status_label.config(text=f"Searching for {search_query}")
            else:
                speak("Please say something to search for.")
                status_label.config(text="Please say something to search for.")
        elif "play music" in response:
            music_query = response.replace("play music", "").strip()  # Remove "play music" da resposta
            if music_query:
                speak(f"Playing {music_query} on YouTube.")
                play_music(music_query)
                status_label.config(text=f"Playing {music_query} on YouTube.")
            else:
                speak("Please say the name of the song you want to play.")
                status_label.config(text="Please say the name of the song you want to play.")
        else:
            speak("Sorry, I can't help with that.")
            status_label.config(text="Sorry, I can't help with that.")

    mic_button.config(state="normal")  # Habilitar o botão novamente


# Criação da janela principal com Tkinter
root = tk.Tk()
root.title("Voice Command AI")

# Definindo o tamanho da janela
root.geometry("400x300")

# Texto de status
status_label = tk.Label(root, text="Click the button to start listening...", font=("Arial", 14))
status_label.pack(pady=20)

# Botão do microfone
mic_button = tk.Button(root, text="🎤 Start Listening", font=("Arial", 16), command=on_mic_button_click)
mic_button.pack(pady=20)

# Iniciar a interface gráfica
root.mainloop()
