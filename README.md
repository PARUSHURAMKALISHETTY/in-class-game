# in-class-game
import pygame
import random

# Initialize Pygame
pygame.init()

# Screen settings
WIDTH, HEIGHT = 700, 700
GRID_SIZE = 7  # 7x7 grid
TILE_SIZE = WIDTH // GRID_SIZE
SCREEN = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Sweet Galaxy")

# Colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
COLORS = [(255, 0, 0), (0, 255, 0), (0, 0, 255), (255, 255, 0), (255, 0, 255)]  # Red, Green, Blue, Yellow, Purple

# Candy class to represent each piece
class Candy:
    def __init__(self, x, y, color):
        self.x = x
        self.y = y
        self.color = color

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, (self.x * TILE_SIZE, self.y * TILE_SIZE, TILE_SIZE - 2, TILE_SIZE - 2))

# Create the game grid
def create_grid():
    return [[Candy(x, y, random.choice(COLORS)) for y in range(GRID_SIZE)] for x in range(GRID_SIZE)]

# Draw the grid
def draw_grid(grid, screen):
    screen.fill(BLACK)
    for row in grid:
        for candy in row:
            candy.draw(screen)

# Check for matches (simplified: horizontal and vertical only)
def check_matches(grid):
    matches = set()
    # Horizontal matches
    for x in range(GRID_SIZE):
        for y in range(GRID_SIZE - 2):
            if grid[x][y].color == grid[x][y + 1].color == grid[x][y + 2].color:
                matches.add((x, y))
                matches.add((x, y + 1))
                matches.add((x, y + 2))
    # Vertical matches
    for x in range(GRID_SIZE - 2):
        for y in range(GRID_SIZE):
            if grid[x][y].color == grid[x + 1][y].color == grid[x + 2][y].color:
                matches.add((x, y))
                matches.add((x + 1, y))
                matches.add((x + 2, y))
    return matches

# Swap candies
def swap_candies(grid, pos1, pos2):
    x1, y1 = pos1
    x2, y2 = pos2
    grid[x1][y1], grid[x2][y2] = grid[x2][y2], grid[x1][y1]

# Main game loop
def main():
    clock = pygame.time.Clock()
    grid = create_grid()
    running = True
    selected = None

    while running:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False
            elif event.type == pygame.MOUSEBUTTONDOWN:
                x, y = event.pos
                grid_x, grid_y = x // TILE_SIZE, y // TILE_SIZE
                if selected is None:
                    selected = (grid_x, grid_y)
                else:
                    # Swap candies if adjacent
                    sx, sy = selected
                    if abs(sx - grid_x) + abs(sy - grid_y) == 1:
                        swap_candies(grid, selected, (grid_x, grid_y))
                        matches = check_matches(grid)
                        if not matches:  # Undo if no match
                            swap_candies(grid, selected, (grid_x, grid_y))
                    selected = None

        # Remove matches and fill (basic version)
        matches = check_matches(grid)
        for x, y in matches:
            grid[x][y] = Candy(x, y, random.choice(COLORS))  # Replace matched candies

        # Draw everything
        draw_grid(grid, SCREEN)
        if selected:
            pygame.draw.rect(SCREEN, WHITE, (selected[0] * TILE_SIZE, selected[1] * TILE_SIZE, TILE_SIZE, TILE_SIZE), 4)
        pygame.display.flip()
        clock.tick(60)

    pygame.quit()

if __name__ == "__main__":
    main()
