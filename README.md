# Rhombix-task-3
import tkinter as tk
from tkinter import messagebox
import random

# Game settings
ROWS = 4
COLS = 4
TIME_LIMIT = 60  # seconds

class MemoryGame:
    def _init(self, root):  # Corrected from _init to _init_
        self.root = root
        self.root.title("Memory Puzzle Game")
        
        self.cards = []
        self.first_card = None
        self.second_card = None
        self.matches_found = 0
        
        self.time_left = TIME_LIMIT
        self.timer_label = tk.Label(root, text=f"Time Left: {self.time_left}s", font=("Arial", 14))
        self.timer_label.grid(row=0, column=0, columnspan=COLS)
        
        self.create_board()
        self.update_timer()

    def create_board(self):
        symbols = list("ABCDEFGH") * 2
        random.shuffle(symbols)
        
        self.cards = []
        for r in range(ROWS):
            row_cards = []
            for c in range(COLS):
                card_value = symbols.pop()
                btn = tk.Button(self.root, text=" ", width=8, height=4, 
                                command=lambda row=r, col=c: self.reveal_card(row, col))
                btn.grid(row=r+1, column=c)  # +1 to leave space for timer
                row_cards.append({"button": btn, "value": card_value, "revealed": False})
            self.cards.append(row_cards)

    def reveal_card(self, r, c):
        card = self.cards[r][c]
        if card["revealed"] or self.second_card:  # ignore clicks on revealed or during match check
            return

        card["button"].config(text=card["value"])
        card["revealed"] = True
        
        if not self.first_card:
            self.first_card = (r, c)
        else:
            self.second_card = (r, c)
            self.root.after(500, self.check_match)  # delay to show both cards

    def check_match(self):
        r1, c1 = self.first_card
        r2, c2 = self.second_card
        card1 = self.cards[r1][c1]
        card2 = self.cards[r2][c2]
        
        if card1["value"] == card2["value"]:
            self.matches_found += 1
            if self.matches_found == (ROWS * COLS) // 2:
                messagebox.showinfo("You Win!", "Congratulations! You matched all pairs.")
                self.root.quit()
        else:
            card1["button"].config(text=" ")
            card2["button"].config(text=" ")
            card1["revealed"] = False
            card2["revealed"] = False
        
        self.first_card = None
        self.second_card = None

    def update_timer(self):
        self.time_left -= 1
        self.timer_label.config(text=f"Time Left: {self.time_left}s")
        if self.time_left <= 0:
            messagebox.showinfo("Time's Up!", "You lost! Better luck next time.")
            self.root.quit()
        else:
            self.root.after(1000, self.update_timer)

# Run the game
root = tk.Tk()
game = MemoryGame(root)
root.mainloop()
