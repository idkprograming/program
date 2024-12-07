#game
import tkinter as tk
import random

class SnakeGame:
    ROWS = 25
    COLS = 25
    TILE_SIZE = 25
    WINDOW_WIDTH = TILE_SIZE * ROWS
    WINDOW_HEIGHT = TILE_SIZE * COLS

    def __init__(self):
        self.window = tk.Tk()
        self.window.title("Snake Game")
        self.window.resizable(False, False)

        self.canvas = tk.Canvas(self.window, bg="blue", width=self.WINDOW_WIDTH, height=self.WINDOW_HEIGHT, borderwidth=0, highlightthickness=0)
        self.start_button = tk.Button(self.window, text="Start Game", command=self.start_game, font="Arial 16")
        self.restart_button = tk.Button(self.window, text="Restart Game", command=self.start_game, font="Arial 16")

        self.center_window()
        self.start_button.pack(pady=20)

        self.window.bind("<KeyRelease>", self.change_direction)

        self.snake = None
        self.food = None
        self.snake_body = []
        self.velocity = [0, 0]
        self.game_over = False
        self.score = 0

    def center_window(self):
        self.window.update_idletasks()
        screen_width = self.window.winfo_screenwidth()
        screen_height = self.window.winfo_screenheight()
        window_x = int((screen_width / 2) - (self.WINDOW_WIDTH / 2))
        window_y = int((screen_height / 2) - (self.WINDOW_HEIGHT / 2))
        self.window.geometry(f"{self.WINDOW_WIDTH}x{self.WINDOW_HEIGHT}+{window_x}+{window_y}")

    def start_game(self):
        self.snake = [5, 5]
        self.food = [10, 10]
        self.snake_body = []
        self.velocity = [0, 0]
        self.game_over = False
        self.score = 0

        self.start_button.pack_forget()
        self.restart_button.pack_forget()
        self.canvas.pack()

        self.draw()

    def change_direction(self, event):
        if self.game_over:
            return

        key = event.keysym
        if key == "Up" and self.velocity[1] != 1:
            self.velocity = [0, -1]
        elif key == "Down" and self.velocity[1] != -1:
            self.velocity = [0, 1]
        elif key == "Left" and self.velocity[0] != 1:
            self.velocity = [-1, 0]
        elif key == "Right" and self.velocity[0] != -1:
            self.velocity = [1, 0]

    def move(self):
        if self.game_over:
            return

        new_head = [self.snake[0] + self.velocity[0], self.snake[1] + self.velocity[1]]

        # Check for wall collision
        if new_head[0] < 0 or new_head[0] >= self.COLS or new_head[1] < 0 or new_head[1] >= self.ROWS:
            self.game_over = True
            return

        # Check for self-collision
        if new_head in self.snake_body:
            self.game_over = True
            return

        self.snake_body.insert(0, self.snake[:])
        self.snake = new_head

        # Check for food collision
        if self.snake == self.food:
            self.score += 1
            self.generate_food()
        else:
            self.snake_body.pop()

    def generate_food(self):
        while True:
            new_food = [random.randint(0, self.COLS - 1), random.randint(0, self.ROWS - 1)]
            if new_food not in self.snake_body and new_food != self.snake:
                self.food = new_food
                break

    def draw(self):
        self.move()
        self.canvas.delete("all")

        # Draw food
        self.canvas.create_rectangle(
            self.food[0] * self.TILE_SIZE, self.food[1] * self.TILE_SIZE,
            (self.food[0] + 1) * self.TILE_SIZE, (self.food[1] + 1) * self.TILE_SIZE,
            fill="red"
        )

        # Draw snake
        self.canvas.create_rectangle(
            self.snake[0] * self.TILE_SIZE, self.snake[1] * self.TILE_SIZE,
            (self.snake[0] + 1) * self.TILE_SIZE, (self.snake[1] + 1) * self.TILE_SIZE,
            fill="lime green"
        )
        
        for segment in self.snake_body:
            self.canvas.create_rectangle(
                segment[0] * self.TILE_SIZE, segment[1] * self.TILE_SIZE,
                (segment[0] + 1) * self.TILE_SIZE, (segment[1] + 1) * self.TILE_SIZE,
                fill="lime green"
            )

        if self.game_over:
            self.canvas.create_text(self.WINDOW_WIDTH / 2, self.WINDOW_HEIGHT / 2, 
                                    font="Arial 20", text=f"GAME OVER: {self.score}", fill="purple")
            self.start_button.pack(pady=10)
            self.restart_button.pack(pady=10)
        else:
            self.canvas.create_text(30, 20, font="Arial 10", text=f"Score: {self.score}", fill="white")
            self.window.after(100, self.draw)

    def run(self):
        self.window.mainloop()

if __name__ == "__main__":
    game = SnakeGame()
    game.run()
