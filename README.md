# Install required packages
!pip install gTTS playsound==1.2.2 pyjokes wikipedia matplotlib --quiet

from gtts import gTTS
import datetime
import webbrowser
import os
import wikipedia
import pyjokes
import IPython.display as ipd
import matplotlib.pyplot as plt
from collections import defaultdict
from IPython.display import HTML

# Function to speak text using gTTS
def speak(text):
    print(f"🔊 {text}")
    tts = gTTS(text=text, lang='en')
    filename = "voice.mp3"
    tts.save(filename)
    ipd.display(ipd.Audio(filename, autoplay=True))

# Command usage tracker
command_counter = defaultdict(int)

# Time and Date Functions
def tell_time():
    command_counter["time"] += 1
    time_now = datetime.datetime.now().strftime("%I:%M %p")
    speak(f"The current time is {time_now}")

def tell_date():
    command_counter["date"] += 1
    date_today = datetime.datetime.now().strftime("%A, %B %d, %Y")
    speak(f"Today's date is {date_today}")

# Web Search
def search_web(query):
    command_counter["search"] += 1
    speak(f"Searching the web for {query}")
    url = f"https://www.google.com/search?q={query}"
    print(f"🌐 Opening: {url}")
    display(HTML(f"<a href='{url}' target='_blank'>🔎 Click here to view search for <b>{query}</b></a>"))
    webbrowser.open(url)

# Wikipedia Summary
def tell_about(topic):
    command_counter["about"] += 1
    try:
        summary = wikipedia.summary(topic, sentences=2)
        print("📘 Wikipedia Summary:")
        display(HTML(f"<div style='border-left: 5px solid #3498db; padding-left: 10px; margin: 10px 0;'>{summary}</div>"))
        speak(summary)
    except Exception:
        speak("Sorry, I couldn't find information on that topic.")

# Joke Function
def tell_joke():
    command_counter["joke"] += 1
    joke = pyjokes.get_joke()
    speak(joke)

# Weather (via search)
def tell_weather(location):
    command_counter["weather"] += 1
    speak(f"Here's the weather in {location}")
    url = f"https://www.google.com/search?q=weather+in+{location}"
    display(HTML(f"<a href='{url}' target='_blank'>☁️ Click here to check weather in <b>{location}</b></a>"))
    webbrowser.open(url)

# Command usage visualization
def show_command_usage():
    if command_counter:
        plt.figure(figsize=(8, 4))
        plt.bar(command_counter.keys(), command_counter.values(), color='skyblue')
        plt.title("Command Usage Summary")
        plt.xlabel("Commands")
        plt.ylabel("Times Used")
        plt.grid(True, axis='y', linestyle='--', alpha=0.6)
        plt.show()

# Main listener function
def listen():
    command = input("\n🎙️ Type your command: ").lower()
    print(f"🗣️ You said: {command}")

    if "hello" in command:
        command_counter["hello"] += 1
        speak("Hello! How can I help you?")
    elif "time" in command:
        tell_time()
    elif "date" in command:
        tell_date()
    elif "joke" in command:
        tell_joke()
    elif "search" in command:
        query = command.replace("search", "").strip()
        if query:
            search_web(query)
        else:
            speak("Please type what you want me to search for.")
    elif "about" in command:
        topic = command.replace("about", "").strip()
        if topic:
            tell_about(topic)
        else:
            speak("Please tell me what you want to know about.")
    elif "weather" in command:
        location = command.replace("weather", "").strip()
        if location:
            tell_weather(location)
        else:
            speak("Please specify a location.")
    elif "exit" in command:
        speak("Goodbye!")
        show_command_usage()
        return False
    else:
        speak("Sorry, I didn't understand that.")
    return True

# Assistant Intro
speak("Hi! I am your voice assistant")
print("""
📚 Example Commands:
- hello
- time
- date
- tell me a joke
- search Python programming
- about Albert Einstein
- weather in New York
- exit
""")

# Main Loop
running = True
while running:
    running = listen()

