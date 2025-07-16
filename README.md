# Python-Project

import tkinter as tk
from tkinter import ttk, messagebox
import webbrowser
import pygame
from pygame import USEREVENT

# Initialize mixer
pygame.mixer.init()

# Custom event for song end
SONG_END = USEREVENT + 1
pygame.mixer.music.set_endevent(SONG_END)

# Data
songs = {
    1: {'singer': 'Arijit Singh', 'song': {
        'romantic': ['Tum Hi Ho', 'Raabta', 'Janam Janam', 'Enna Sona', 'Hawayein'],
        'sad': ['Channa Mereya', 'Agar Tum Saath Ho', 'Hamari Adhuri Kahani', 'Khairiyat', 'Tujhe Chahne Lage'],
        'party': ['Badtmeez Dil', 'Nashe Si Chadh Gayi', 'Ghungroo', 'Suraj Dooba Hai'],
        'devotional': ['Mast Magan', 'Duaa', 'Lal Ishq'],
        'melody': ['Ae Watan', 'Phir Le Aya Dil', 'Zaalima', 'Muskurane']
    }},
    2: {'singer': 'Shreya Ghoshal', 'song': {
        'romantic': ['Teri Meri', 'Sun Raha Hai', 'Agar Tum Mil Jao', 'Saans', 'Pal'],
        'sad': ['Bahara', 'Mere Dholna', 'Jadu Hai Nasha Hai', 'Laagi Lagan'],
        'party': ['Chikni Chameli', 'Ooh La La', 'Radha', 'Balam Pichkari'],
        'devotional': ['Mere Dholna', 'O Palan Hare', 'Manwa Laage'],
        'melody': ['Tujh Mein Rab Dikhta Hai', 'Piyu Bole', 'Raabta']
    }},
    3: {'singer': 'Diljit Dosanjh', 'song': {
        'party': ['Do You Know', '5 Taara', 'Proper Patola', 'Lover', 'G.O.A.T', 'Laembadgini'],
        'romantic': ['Ishq Hazir Hai', 'Jind Maahi', 'Ek Kudi', 'Nakhre', 'Kinni Kinni'],
        'emotional': ['Putt Jatt Da', 'Jatt Fire Karda', 'Raat Di Gedi', 'Dil Tutda', 'Tera Naam'],
        'fusion': ['Ikk Kudi', 'Move Your Lakk', 'Sauda Khara Khara', 'Lover'],
        'motivational': ['Born to Shine', 'Big Fan', 'Clash']
    }},
    4: {'singer': 'Sonu Nigam', 'song': {
        'romantic': ['Suraj Hua Maddham', 'Abhi Mujh Mein Kahin', 'Saathiya', 'Inn Lamhon Ke Daaman Mein', 'Meri Duniya Hai'],
        'sad': ['Tanhayee', 'Satrangi Re', 'Kal Ho Naa Ho', 'Main Agar Kahoon', 'Chhoti Chhoti Raatein'],
        'energetic': ['Mujhse Shaadi Karogi', 'Desi Girl', 'Shukran Allah', 'Deewana Tera'],
        'devotional': ['Sandese Aate Hain', 'Shirdi Wale Sai Baba', 'Maa', 'Jai Jai Ganesh Deva'],
        'melody': ['Bheege Honth Tere', 'Chanda Ki Doli', 'Sapna Jahan']
    }},
    5: {'singer': 'Sunidhi Chauhan', 'song': {
        'party': ['Sheila Ki Jawani', 'Desi Girl', 'Crazy Kiya Re', 'Dhoom Machale'],
        'romantic': ['Meri Zindagi Mein', 'Bhumaro', 'Bin Tere', 'Bhage Re Mann'],
        'emotional': ['Aa Zara', 'Yaaram', 'Darkhaast', 'Mehboob Mere'],
        'melody': ['Ruki Ruki Si Zindagi', 'Kaisi Paheli Zindgani', 'Ghoomar', 'Here Is Beautiful']
    }}
}

song_path = {
    "Tum Hi Ho": "TumHiHo.mp3",
    "Raabta": "https://youtu.be/zlt38OOqwDc",
    "Teri Meri": "https://youtu.be/VU5kQzVnIzA",
    "Lover": "https://youtu.be/WoXDFQxkppU",
    "Desi Girl": "https://youtu.be/gEhDti7zUUg",
    "Sheila Ki Jawani": "https://youtu.be/ZtmW7vJ7Ugw"
    # Add more as needed
}

playlist = []
current_song_index = 0

# GUI Setup
root = tk.Tk()
root.title("Song Selector")
root.geometry("600x600")

singer_var = tk.StringVar()
mood_var = tk.StringVar()
song_var = tk.StringVar()

def update_moods(*args):
    singer_id = next((key for key, val in songs.items() if val['singer'] == singer_var.get()), None)
    if singer_id:
        moods = list(songs[singer_id]['song'].keys())
        mood_cb['values'] = moods
        mood_cb.set('')

def show_songs():
    song_listbox.delete(0, tk.END)
    selected_singer = singer_var.get()
    selected_mood = mood_var.get()
    if not selected_singer or not selected_mood:
        messagebox.showerror("Error", "Please select both singer and mood.")
        return
    singer_id = next((key for key, val in songs.items() if val['singer'] == selected_singer), None)
    if singer_id:
        mood_songs = songs[singer_id]['song'].get(selected_mood, [])
        for song in mood_songs:
            song_listbox.insert(tk.END, song)

def add_to_playlist():
    selected = song_listbox.curselection()
    for index in selected:
        song = song_listbox.get(index)
        if song not in playlist:
            playlist.append(song)
            playlist_listbox.insert(tk.END, song)

def play_selected_song():
    global current_song_index
    selected = playlist_listbox.curselection()
    if not selected:
        messagebox.showinfo("No Selection", "Please select a song from your playlist to play.")
        return
    current_song_index = selected[0]
    song = playlist[current_song_index]
    play_song(song)

def play_song(song):
    if song in song_path:
        path = song_path[song]
        if path.endswith(".mp3"):
            try:
                pygame.mixer.music.load(path)
                pygame.mixer.music.play()
                messagebox.showinfo("Playing", f"Now playing: {song}")
            except Exception as e:
                messagebox.showerror("Error", f"Could not play the song:\n{e}")
        elif path.startswith("http"):
            webbrowser.open(path)
        else:
            messagebox.showwarning("Unknown Format", "This song format is not supported.")
    else:
        messagebox.showwarning("Unavailable", "No path found for this song.")

def play_next_song():
    global current_song_index
    current_song_index += 1
    if current_song_index < len(playlist):
        song = playlist[current_song_index]
        playlist_listbox.selection_clear(0, tk.END)
        playlist_listbox.selection_set(current_song_index)
        playlist_listbox.activate(current_song_index)
        play_song(song)
    else:
        current_song_index = 0

def pause_song():
    pygame.mixer.music.pause()

def unpause_song():
    pygame.mixer.music.unpause()

def stop_song():
    pygame.mixer.music.stop()

def check_song_end():
    for event in pygame.event.get():
        if event.type == SONG_END:
            play_next_song()
    root.after(1000, check_song_end)

# Start checking for song end
root.after(1000, check_song_end)

# UI Layout
ttk.Label(root, text="Select Singer:").pack(pady=5)
singer_cb = ttk.Combobox(root, textvariable=singer_var, values=[v['singer'] for v in songs.values()])
singer_cb.pack()
singer_cb.bind('<<ComboboxSelected>>', update_moods)

ttk.Label(root, text="Select Mood:").pack(pady=5)
mood_cb = ttk.Combobox(root, textvariable=mood_var)
mood_cb.pack()

ttk.Button(root, text="Show Songs", command=show_songs).pack(pady=10)

song_listbox = tk.Listbox(root, selectmode=tk.MULTIPLE, height=8)
song_listbox.pack(pady=5)

ttk.Button(root, text="Add to Playlist", command=add_to_playlist).pack(pady=10)

ttk.Label(root, text="My Playlist:").pack()
playlist_listbox = tk.Listbox(root, height=8)
playlist_listbox.pack(pady=5)

ttk.Button(root, text="Play Selected Song", command=play_selected_song).pack(pady=5)
ttk.Button(root, text="Pause", command=pause_song).pack(pady=2)
ttk.Button(root, text="Resume", command=unpause_song).pack(pady=2)
ttk.Button(root, text="Stop", command=stop_song).pack(pady=2)

root.mainloop()
