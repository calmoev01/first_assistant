import speech_recognition as sr
import sounddevice as sd
import numpy as np
from gtts import gTTS
import playsound
import webbrowser
import os
import tempfile

def talk(text, lang="en"):
    print("[AI]:", text)
    tts = gTTS(text=text, lang=lang)
    tmp_file = tempfile.mktemp(suffix=".mp3")
    tts.save(tmp_file)
    playsound.playsound(tmp_file)
    os.remove(tmp_file)

def listen_command():
    recognizer = sr.Recognizer()
    duration = 5
    fs = 16000
    print("Speak...")
    audio_data = sd.rec(int(duration * fs), samplerate=fs, channels=1, dtype='int16')
    sd.wait()
    audio = sr.AudioData(np.array(audio_data).tobytes(), fs, 2)

    try:
        command = recognizer.recognize_google(audio, language="en-US").lower()
        print("You said:", command)
        return command
    except sr.UnknownValueError:
        talk("could you repeat")
        return ""
    except sr.RequestError:
        talk("Speech recognition service error")
        return ""

while True:
    cmd = listen_command()
    if not cmd:
        continue

    if "youtube" in cmd:
        talk("Opening YouTube")
        webbrowser.open("https://www.youtube.com")

    elif "google" in cmd:
        talk("Opening Google")
        webbrowser.open("https://www.google.com")

    elif "stop" in cmd or "exit" in cmd or "close" in cmd:
        talk("Goodbye calmoev!")
        break

    else:
        talk("Command not recognized")
