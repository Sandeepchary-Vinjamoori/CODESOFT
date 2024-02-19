# CODESOFT
# This repository contains projects completed during my AI internship at CodeSoft. It includes implementations of face detection, recommendation systems, and chatbot development using Python and various AI techniques.

import json
from difflib import get_close_matches

R_EATING = "I don't like eating anything because I'm a bot obviously!"
R_ADVICE = "Sorry, I'm just a chatbot, not good at giving advice."

def unknown():
    return "Sorry, I don't know."

def message_probability(user_message, recognised_words, single_response=False, required_words=[]):
    message_certainty = 0
    has_required_words = True

    for word in user_message:
        if word in recognised_words:
            message_certainty += 1

    percentage = float(message_certainty) / float(len(recognised_words))

    for word in required_words:
        if word not in user_message:
            has_required_words = False
            break

    if has_required_words or single_response:
        return int(percentage * 100)
    else:
        return 0

knowledge_base = {
    "questions": []
}

def find_best_match(user_question, questions):
    matches = get_close_matches(user_question, questions, n=1, cutoff=0.6)
    return matches[0] if matches else None

def get_answer_for_question(question, knowledge_base):
    for q in knowledge_base["questions"]:
        if q["question"] == question:
            return q["answer"]
    return None

def check_all_messages(message):
    highest_prob_list = {}

    responses = {
        'Hello!': ['hello', 'hi', 'hey', 'sup', 'heyo'],
        'See you!': ['bye', 'goodbye'],
        'I\'m doing fine, and you?': ['how', 'are', 'you', 'doing'],
        'You\'re welcome!': ['thank', 'thanks'],
        'Thank you!': ['i', 'love', 'code', 'palace'],
        'I love learning new things!': ['habit', 'good'],
        'I try to stay away from bad habits.': ['habit', 'bad'],
        'My interests include chatting and helping users.': ['interests'],
        'I enjoy playing text-based games. Would you like to play one?': ['games', 'play'],
        'The capital of France is Paris.': ['capital', 'france'],
        'Water boils at 100 degrees Celsius.': ['water', 'boils'],
        'The Earth orbits around the Sun.': ['earth', 'orbits'],
        'The largest mammal is the blue whale.': ['largest', 'mammal'],
        'The speed of light is approximately 299,792 kilometers per second.': ['speed', 'light']
    }

    for response, words in responses.items():
        highest_prob_list[response] = message_probability(message, words)

    best_match = max(highest_prob_list, key=highest_prob_list.get)
    return unknown() if highest_prob_list[best_match] < 1 else best_match

def chat_bot(user_input):
    response = check_all_messages(user_input.lower().split())
    if response != unknown():
        return response
    else:
        best_match = find_best_match(user_input, [q["question"] for q in knowledge_base["questions"]])
        if best_match:
            return get_answer_for_question(best_match, knowledge_base)
        else:
            return "I don't know the answer. Can you teach me?"
