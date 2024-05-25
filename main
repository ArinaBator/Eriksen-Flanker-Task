import os
import csv
import random
import time
from psychopy import visual, core, event, monitors

# Path to the "results" folder on the desktop
desktop_path = os.path.join(os.path.expanduser("~"), "Desktop")
results_folder = os.path.join(desktop_path, "wyniki")

# Checking and creating the result folder if it doesn't exist
if not os.path.exists(results_folder):
    os.makedirs(results_folder)

mon = monitors.Monitor('testMonitor')
mon.setSizePix((0, 0))

win = visual.Window(size=(1920, 1080), monitor=mon, screen=0, fullscr=True, color="#DDDBDD", units='height')

# Hiding the cursor
win.mouseVisible = False

# Colors
black = "#000000"

# Types of stimuli
zgodny_stimuli = ['→→→→→', '←←←←←']
niezgodny_stimuli = ['→→←→→', '←←→←←']
neutralny_stimuli = ['→', '←']

# Frequency of stimuli occurrence
stimuli_types = ['zgodny'] * 40 + ['niezgodny'] * 40 + ['neutralny'] * 20
random.shuffle(stimuli_types)

# Graphic characteristics of stimuli
fixation_height = 0.1
stimuli_height = 0.1 

pause = visual.TextStim(win, text='  ')
fixation = visual.TextStim(win, text='+', color=black, height=fixation_height)
stimuli = visual.TextStim(win, text='', color=black, height=stimuli_height)

# Instructions and messages for the participant
instruction_text = """
Instrukcja wykonania zadania

W zadaniu, które za chwilę wykonasz, na ekranie pojawiać się będą różne sekwencje, składające się z jednej lub pięciu strzałek. Twoim zadaniem jest wskazanie kierunku strzałki środkowej ignorując strzałki znajdujące się po jej bokach (jeżeli takowe w danej próbie występują). Jeżeli środkowa strzałka skierowana jest w lewą stronę wciśnij lewy klawisz kierunkowy („lewą strzałkę na klawiaturze”), jeżeli środkowa strzałka skierowana jest w prawą stronę wciśnij prawy klawisz kierunkowy („prawą strzałkę na klawiaturze”).

Postaraj się odpowiadać poprawnie jak najszybciej, gdyż w zadaniu mierzony będzie czas oraz poprawność Twojej reakcji.

Zadanie składa się z dwóch sesji: treningowej i eksperymentalnej. Sesja eksperymentalna składa się z 3 części złożonych z 60 prób każda.

Zanim rozpocznie się sesja eksperymentalna, zostaniesz poddany sesji treningowej, która ułatwi Ci zrozumienie zadania. W trakcie sesji treningowej, po każdej udzielonej odpowiedzi zostaniesz poinformowany o tym, czy Twoja odpowiedź była poprawna czy błędna. Informacja o poprawności nie będzie się pojawiać w sesji eksperymentalnej.

Pamiętaj, że każdy problem lub wątpliwość możesz zgłosić eksperymentatorowi.

Aby przejść dalej naciśnij spację.
"""

break_text = """
Przerwa

Kiedy będziesz gotowy, aby przejść do kolejnej części, naciśnij spację.
"""

reminder_text = """
Zadanie zaraz się rozpocznie.
Skup swój wzrok na znaku „+” i pamiętaj by udzielać odpowiedzi jak najszybciej. 
Gdy będziesz gotowy/a naciśnij spację.
"""

end_text = """
To już koniec badania. Serdecznie dziękujemy za wzięcie w nim udziału!
"""

def save_results(results):
    max_participant_id = 0
    for filename in os.listdir(results_folder):
        if filename.startswith("wynik_") and filename.endswith(".csv"):
            try:
                participant_num = int(filename.split("_")[1].split(".")[0])
                if participant_num > max_participant_id:
                    max_participant_id = participant_num
            except ValueError:
                continue
    
    
   # Determining the participant number for the current session
    participant_id = max_participant_id + 1
    filename = os.path.join(results_folder, f"wynik_{participant_id}.csv")
    
    # Saving results to a CSV file
    with open(filename, mode='w', newline='') as file:
        writer = csv.writer(file)
        writer.writerow(["nazwa sesji", "numer próby", "bodziec", "wciśnięty klawisz (L - lewy przycisk kierunkowy, P - prawy przycisk kierunkowy", "poprawność reakcji", "czas reakcji [ms]"])
        writer.writerows(results)

# Displaying instructions
win.mouseVisible = False

instruction = visual.TextStim(win, text=instruction_text, color=black, height=0.035, wrapWidth=1.5)
instruction.draw()
win.flip()
keys = event.waitKeys(keyList=['space', 'f7'])

if 'f7' in keys:
    win.close()
    core.quit()

results = []

def run_trial(session, trial_num, part=None, training=False):
    stim_type = random.choice(stimuli_types)
    if stim_type == 'zgodny':
        stim = random.choice(zgodny_stimuli)
    elif stim_type == 'niezgodny':
        stim = random.choice(niezgodny_stimuli)
    else:
        stim = random.choice(neutralny_stimuli)

    fixation.draw()
    win.flip()
    core.wait(0.8)

    stimuli.setText(stim)
    stimuli.draw()
    win.flip()
    start_time = time.time()
    keys = event.waitKeys(keyList=['left', 'right', 'f7'], maxWait=4)

    reaction_time = round((time.time() - start_time) * 1000)

    if keys and 'f7' in keys:
        save_results(results)
        win.close()
        core.quit()

    pressed_key = None
    accuracy = None
    feedback_text = ""

    if keys:
        pressed_key = 'L' if keys[0] == 'left' else 'P'
        if stim_type == 'zgodny':
            correct_key = 'P' if stim == '→→→→→' else 'L'
        elif stim_type == 'niezgodny':
            correct_key = 'P' if stim == '←←→←←' else 'L'
        else:
            correct_key = 'P' if stim == '→' else 'L'

        accuracy = 'Poprawna' if pressed_key == correct_key else 'Niepoprawna'
        feedback_text = "Dobrze" if accuracy == 'Poprawna' else "Źle"
    else:
        feedback_text = "Za wolno"
        accuracy = "Timeout"

    if training:
        feedback = visual.TextStim(win, text=feedback_text, color=black, height=0.07)
        feedback.draw()
        win.flip()
        core.wait(2.0)

    session_name = session
    if part is not None:
        session_name += f" - część {part}"

    # Adding a result to the list
    results.append([session_name, trial_num, stim_type, pressed_key, accuracy, reaction_time])

# Training session
instruction = visual.TextStim(win, text="Trening", color=black, height=0.07)
instruction.draw()
win.flip()
core.wait(2.0)

# Reminder 
instruction = visual.TextStim(win, text=reminder_text, color=black, height=0.07, wrapWidth=1.5)
instruction.draw()
win.flip()
event.waitKeys(keyList=['space'])

for trial_num in range(10):
    run_trial("Trening", trial_num + 1, training=True)
    
    pause.draw()
    win.flip()
    core.wait(0.8)

# Break
break_screen = visual.TextStim(win, text=break_text, color=black, height=0.07)
break_screen.draw()
win.flip()
event.waitKeys(keyList=['space'])

# Experimental session, parts 1-2
for part in range(1, 3):
    instruction = visual.TextStim(win, text=f"Sesja eksperymentalna – część {part}", color=black, height=0.07)
    instruction.draw()
    win.flip()
    core.wait(2.0)

    instruction = visual.TextStim(win, text=reminder_text, color=black, height=0.07, wrapWidth=1.5)
    instruction.draw()
    win.flip()
    event.waitKeys(keyList=['space'])

    for trial_num in range(60):
        run_trial("Sesja eksperymentalna", trial_num + 1, part)
        
        pause.draw()
        win.flip()
        core.wait(0.8)

    break_screen = visual.TextStim(win, text=break_text, color=black, height=0.07)
    break_screen.draw()
    win.flip()
    event.waitKeys(keyList=['space'])


# Experimental session, parts 3
instruction = visual.TextStim(win, text="Sesja eksperymentalna – część 3", color=black, height=0.07)
instruction.draw()
win.flip()
core.wait(2.0)

instruction = visual.TextStim(win, text=reminder_text, color=black, height=0.07, wrapWidth=1.5)
instruction.draw()
win.flip()
event.waitKeys(keyList=['space'])

for trial_num in range(60):
    run_trial("Sesja eksperymentalna", trial_num + 1, part=3)

# Saving the results of the experimental session
save_results(results)

# Final message
end_screen = visual.TextStim(win, text=end_text, color=black, height=0.07)
end_screen.draw()
win.flip()
core.wait(3.0)

win.close()
