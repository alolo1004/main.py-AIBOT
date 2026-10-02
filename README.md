import random
import re

import sounddevice as sd
import scipy.io.wavfile as wav
import speech_recognition as sr


# ==============================
# CONFIGURAÇÕES
# ==============================

DURATION = 8
SAMPLE_RATE = 44100
AUDIO_FILE = "output.wav"
ROUNDS = 8

# ==============================
# PALAVRAS E TRADUÇÕES
# ==============================

WORDS = {

    "facil": {

        "casa": {
            "ingles": "house",
            "espanhol": "casa"
        },

        "gato": {
            "ingles": "cat",
            "espanhol": "gato"
        },

        "cachorro": {
            "ingles": "dog",
            "espanhol": "perro"
        },

        "água": {
            "ingles": "water",
            "espanhol": "agua"
        },

        "livro": {
            "ingles": "book",
            "espanhol": "libro"
        },

        "carro": {
            "ingles": "car",
            "espanhol": "coche"
        },

        "sol": {
            "ingles": "sun",
            "espanhol": "sol"
        },

        "escola": {
            "ingles": "school",
            "espanhol": "escuela"
        },

        "mesa": {
            "ingles": "table",
            "espanhol": "mesa"
        },

        "cadeira": {
            "ingles": "chair",
            "espanhol": "silla"
        },

        "porta": {
            "ingles": "door",
            "espanhol": "puerta"
        },

        "maçã": {
            "ingles": "apple",
            "espanhol": "manzana"
        },

        "banana": {
            "ingles": "banana",
            "espanhol": "plátano"
        },

        "leite": {
            "ingles": "milk",
            "espanhol": "leche"
        },

        "amigo": {
            "ingles": "friend",
            "espanhol": "amigo"
        },

        "família": {
            "ingles": "family",
            "espanhol": "familia"
        },

        "dia": {
            "ingles": "day",
            "espanhol": "día"
        },

        "noite": {
            "ingles": "night",
            "espanhol": "noche"
        },

        "feliz": {
            "ingles": "happy",
            "espanhol": "feliz"
        },

        "grande": {
            "ingles": "big",
            "espanhol": "grande"
        }
    },


    "medio": {

        "janela": {
            "ingles": "window",
            "espanhol": "ventana"
        },

        "computador": {
            "ingles": "computer",
            "espanhol": "computadora"
        },

        "bicicleta": {
            "ingles": "bicycle",
            "espanhol": "bicicleta"
        },

        "montanha": {
            "ingles": "mountain",
            "espanhol": "montaña"
        },

        "professor": {
            "ingles": "teacher",
            "espanhol": "profesor"
        },

        "amizade": {
            "ingles": "friendship",
            "espanhol": "amistad"
        },

        "bonito": {
            "ingles": "beautiful",
            "espanhol": "bonito"
        },

        "importante": {
            "ingles": "important",
            "espanhol": "importante"
        },

        "cidade": {
            "ingles": "city",
            "espanhol": "ciudad"
        },

        "viagem": {
            "ingles": "travel",
            "espanhol": "viaje"
        },

        "música": {
            "ingles": "music",
            "espanhol": "música"
        },

        "filme": {
            "ingles": "movie",
            "espanhol": "película"
        },

        "jardim": {
            "ingles": "garden",
            "espanhol": "jardín"
        },

        "trabalho": {
            "ingles": "work",
            "espanhol": "trabajo"
        },

        "esporte": {
            "ingles": "sport",
            "espanhol": "deporte"
        },

        "história": {
            "ingles": "history",
            "espanhol": "historia"
        },

        "pergunta": {
            "ingles": "question",
            "espanhol": "pregunta"
        },

        "resposta": {
            "ingles": "answer",
            "espanhol": "respuesta"
        },

        "problema": {
            "ingles": "problem",
            "espanhol": "problema"
        },

        "feriado": {
            "ingles": "holiday",
            "espanhol": "vacaciones"
        }
    },


    "dificil": {

        "liberdade": {
            "ingles": "freedom",
            "espanhol": "libertad"
        },

        "conhecimento": {
            "ingles": "knowledge",
            "espanhol": "conocimiento"
        },

        "responsabilidade": {
            "ingles": "responsibility",
            "espanhol": "responsabilidad"
        },

        "desenvolvimento": {
            "ingles": "development",
            "espanhol": "desarrollo"
        },

        "possibilidade": {
            "ingles": "possibility",
            "espanhol": "posibilidad"
        },

        "comunicação": {
            "ingles": "communication",
            "espanhol": "comunicación"
        },

        "criatividade": {
            "ingles": "creativity",
            "espanhol": "creatividad"
        },

        "ambiente": {
            "ingles": "environment",
            "espanhol": "medio ambiente"
        },

        "experiência": {
            "ingles": "experience",
            "espanhol": "experiencia"
        },

        "educação": {
            "ingles": "education",
            "espanhol": "educación"
        },

        "tecnologia": {
            "ingles": "technology",
            "espanhol": "tecnología"
        },

        "sociedade": {
            "ingles": "society",
            "espanhol": "sociedad"
        },

        "oportunidade": {
            "ingles": "opportunity",
            "espanhol": "oportunidad"
        },

        "construção": {
            "ingles": "construction",
            "espanhol": "construcción"
        },

        "informação": {
            "ingles": "information",
            "espanhol": "información"
        },

        "imaginação": {
            "ingles": "imagination",
            "espanhol": "imaginación"
        },

        "personalidade": {
            "ingles": "personality",
            "espanhol": "personalidad"
        },

        "independência": {
            "ingles": "independence",
            "espanhol": "independencia"
        },

        "determinação": {
            "ingles": "determination",
            "espanhol": "determinación"
        },

        "concentração": {
            "ingles": "concentration",
            "espanhol": "concentración"
        }
    }
}


# ==============================
# CONFIGURAÇÃO DOS IDIOMAS
# ==============================

LANGUAGES = {
    "1": {
        "nome": "Inglês",
        "codigo": "ingles",
        "reconhecimento": "en-US"
    },

    "2": {
        "nome": "Espanhol",
        "codigo": "espanhol",
        "reconhecimento": "es-ES"
    }
}


# ==============================
# TELA INICIAL
# ==============================

def show_title():

    print()
    print("=" * 50)
    print("              SPEAK RIGHT")
    print("=" * 50)
    print("       Teste sua pronúncia em idiomas!")
    print("=" * 50)
    print()


# ==============================
# ESCOLHER IDIOMA
# ==============================

def choose_language():

    print("Escolha o idioma:")
    print()
    print("1 - Inglês")
    print("2 - Espanhol")
    print()

    while True:

        choice = input("Digite 1 ou 2: ").strip()

        if choice in LANGUAGES:

            return LANGUAGES[choice]

        else:

            print()
            print("Opção inválida.")
            print("Digite 1 para Inglês ou 2 para Espanhol.")
            print()


# ==============================
# ESCOLHER DIFICULDADE
# ==============================

def choose_difficulty():

    print()
    print("Escolha a dificuldade:")
    print()
    print("1 - Fácil")
    print("2 - Médio")
    print("3 - Difícil")
    print()

    while True:

        choice = input("Digite 1, 2 ou 3: ").strip()

        if choice == "1":
            return "facil"

        elif choice == "2":
            return "medio"

        elif choice == "3":
            return "dificil"

        else:

            print()
            print("Opção inválida.")
            print("Digite 1, 2 ou 3.")
            print()


# ==============================
# GRAVAR ÁUDIO
# ==============================

def record_audio():

    print()
    print("🎙️ Fale agora!")
    print(f"Você tem {DURATION} segundos.")
    print()

    recording = sd.rec(
        int(DURATION * SAMPLE_RATE),
        samplerate=SAMPLE_RATE,
        channels=1,
        dtype="int16"
    )

    sd.wait()

    wav.write(
        AUDIO_FILE,
        SAMPLE_RATE,
        recording
    )

    print("Gravação concluída.")


# ==============================
# RECONHECER FALA
# ==============================

def recognize_speech(language_code):

    recognizer = sr.Recognizer()

    try:

        with sr.AudioFile(AUDIO_FILE) as source:

            audio = recognizer.record(source)

        text = recognizer.recognize_google(
            audio,
            language=language_code
        )

        return text

    except sr.UnknownValueError:

        return None

    except sr.RequestError as error:

        print()
        print("Erro no serviço de reconhecimento de voz.")
        print(error)

        return None


# ==============================
# NORMALIZAR TEXTO
# ==============================

def normalize_text(text):

    text = text.lower().strip()

    text = re.sub(
        r"[^\w\s]",
        "",
        text
    )

    return text


# ==============================
# JOGAR
# ==============================

def play_game(difficulty, language):

    score = 0

    used_words = []

    words = WORDS[difficulty]

    language_name = language["nome"]
    language_code = language["codigo"]
    recognition_language = language["reconhecimento"]

    print()
    print("=" * 50)
    print(f"        IDIOMA: {language_name.upper()}")
    print(f"        DIFICULDADE: {difficulty.upper()}")
    print(f"        RODADAS: {ROUNDS}")
    print("=" * 50)

    for round_number in range(1, ROUNDS + 1):

        # Escolher palavra ainda não usada
        available_words = [
            word
            for word in words
            if word not in used_words
        ]

        portuguese_word = random.choice(
            available_words
        )

        used_words.append(
            portuguese_word
        )

        # Tradução
        translated_word = words[
            portuguese_word
        ][language_code]

        # ------------------------------
        # MOSTRAR PALAVRA
        # ------------------------------

        print()
        print("-" * 50)
        print(f"RODADA {round_number}/{ROUNDS}")
        print("-" * 50)

        print()

        print("🇧🇷 Palavra:")

        print()
        print(
            f"             {portuguese_word.upper()}"
        )

        print()

        print(
            f"🌎 Tradução: {translated_word.upper()}"
        )

        print()
        print(f"Pontuação atual: {score}")
        print()

        input(
            "Pressione ENTER para começar..."
        )

        # ------------------------------
        # GRAVAR
        # ------------------------------

        record_audio()

        # ------------------------------
        # RECONHECER
        # ------------------------------

        print()
        print("🔎 Analisando sua pronúncia...")

        spoken_text = recognize_speech(
            recognition_language
        )

        print()

        # ------------------------------
        # VERIFICAR
        # ------------------------------

        if spoken_text is None:

            print(
                "❌ Não consegui entender sua fala."
            )

            print()
            print(
                f"A resposta era: {translated_word}"
            )

            continue

        spoken_normalized = normalize_text(
            spoken_text
        )

        expected_normalized = normalize_text(
            translated_word
        )

        print(
            f"Você falou: {spoken_text}"
        )

        print(
            f"Esperado:   {translated_word}"
        )

        print()

        if spoken_normalized == expected_normalized:

            score += 10

            print("✅ CORRETO!")
            print("+10 pontos")
            print(
                f"Pontuação: {score}"
            )

        else:

            print("❌ ERRADO!")
            print("+0 pontos")

            print(
                f"A resposta correta era: "
                f"{translated_word}"
            )

            print(
                f"Pontuação: {score}"
            )

    # ==============================
    # FIM DO JOGO
    # ==============================

    print()
    print("=" * 50)
    print("              FIM DE JOGO")
    print("=" * 50)

    print()

    print(
        f"Idioma: {language_name}"
    )

    print(
        f"Dificuldade: {difficulty}"
    )

    print(
        f"Pontuação final: {score}/{ROUNDS * 10}"
    )

    print()

    if score == ROUNDS * 10:

        print("🏆 PERFEITO!")
        print(
            "Você acertou todas as palavras!"
        )

    elif score >= 30:

        print("👏 Muito bom!")
        print(
            "Você teve um ótimo desempenho."
        )

    elif score >= 10:

        print("📚 Bom trabalho!")
        print(
            "Continue praticando sua pronúncia."
        )

    else:

        print("💪 Continue treinando!")
        print(
            "A prática melhora sua pronúncia."
        )

    print()
    print("=" * 50)


# ==============================
# PROGRAMA PRINCIPAL
# ==============================

def main():

    show_title()

    language = choose_language()

    difficulty = choose_difficulty()

    print()
    print("Preparando o jogo...")
    print()

    play_game(
        difficulty,
        language
    )


# ==============================
# INICIAR
# ==============================

if __name__ == "__main__":

    main()
