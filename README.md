import os
import openai

# Set up OpenAI API key from environment variable
openai.api_key = os.getenv('OPENAI_API_KEY')

def fetch_keywords(text):
    # OpenAI API call to extract keywords
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=f"Extract significant keywords from the following text:\n{text}\nKeywords:",
        max_tokens=50,
        n=1,
        stop=None,
        temperature=0.5
    )
    keywords = response.choices[0].text.strip().split(',')
    return [kw.strip() for kw in keywords]

def fetch_idioms(text):
    # OpenAI API call to extract idioms
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=f"Extract idioms or common expressions from the following text:\n{text}\nIdioms:",
        max_tokens=50,
        n=1,
        stop=None,
        temperature=0.5
    )
    idioms = response.choices[0].text.strip().split(',')
    return [idiom.strip() for idiom in idioms]

def fetch_emotions(text):
    # OpenAI API call to extract emotions
    response = openai.Completion.create(
        engine="text-davinci-003",
        prompt=f"Identify the primary emotions conveyed in the following text:\n{text}\nEmotions:",
        max_tokens=50,
        n=1,
        stop=None,
        temperature=0.5
    )
    emotions = response.choices[0].text.strip().split(',')
    return [emotion.strip() for emotion in emotions]

def construct_song(keywords, idioms, emotions):
    # Construct a song based on fetched keywords, idioms, and emotions
    if not emotions:
        song_title = "Untitled"
    else:
        song_title = emotions[0].capitalize()

    verses = []
    chorus = []
    
    # Create verses from keywords (odd lines)
    for i in range(0, len(keywords), 4):
        verse = ', '.join(keywords[i:i+4])
        verses.append(verse)
    
    # Create chorus from idioms
    for idiom in idioms:
        chorus.append(f"{idiom}, {idiom} with all my might.")
    
    # Construct song text
    song = f"**Song: {song_title}**\n\n"
    for verse in verses:
        song += verse + "\n\n"
    
    song += "\n".join(chorus) + "\n\n"
    
    return song

def text_to_song(text):
    # Fetch keywords, idioms, and emotions
    keywords = fetch_keywords(text)
    idioms = fetch_idioms(text)
    emotions = fetch_emotions(text)
    
    # Construct the song
    song = construct_song(keywords, idioms, emotions)
    return song

def read_file(file_path):
    # Read text from the input file
    with open(file_path, 'r') as file:
        return file.read()

# Main function
if __name__ == "__main__":
    # Specify the path to the input text file
    input_file_path = 'input.txt'

    # Read the input text from file
    input_text = read_file(input_file_path)

    # Transform text to song
    song = text_to_song(input_text)

    # Output the generated song
    print(song)
