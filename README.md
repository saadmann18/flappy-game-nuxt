# Flappy Bird Game

A fun and engaging Flappy Bird game built with Nuxt 3, Vue 3, and TypeScript. Navigate through pipes by clicking or pressing spacebar to flap your wings!

## Features

- 🐦 Classic Flappy Bird gameplay mechanics
- 🎮 Click or spacebar controls
- 🏆 Score tracking with local storage persistence
- 📱 Responsive design with beautiful animations
- 🎨 Modern UI with Tailwind CSS
- ⚡ Built with Nuxt 3 and TypeScript for optimal performance

## Technology Stack

- **Framework**: Nuxt 3
- **Frontend**: Vue 3 with Composition API
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **Game Canvas**: HTML5 Canvas API

## Getting Started

### Prerequisites

- Node.js (v18 or higher)
- npm or yarn package manager

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd flappy-bird-game
```

2. Install dependencies:
```bash
npm install
```

3. Start the development server:
```bash
npm run dev
```

4. Open your browser and navigate to `http://localhost:3000`

## Scripts

- `npm run dev` - Start development server
- `npm run build` - Build for production
- `npm run generate` - Generate static site
- `npm run preview` - Preview production build

## Game Controls

- **Click** on the game canvas to make the bird flap
- **Spacebar** to make the bird flap
- Navigate through the green pipes without hitting them
- Try to achieve the highest score possible!

## Project Structure

```
├── app/
│   └── app.vue          # Main app component
├── pages/
│   ├── index.vue        # Home page with game intro
│   └── game.vue         # Main game component
├── public/
│   ├── favicon.ico      # Site favicon
│   └── robots.txt       # SEO robots file
├── nuxt.config.ts       # Nuxt configuration
├── package.json         # Dependencies and scripts
└── tsconfig.json        # TypeScript configuration
```

## Game Mechanics

- **Bird Physics**: Gravity and flap mechanics
- **Pipe Generation**: Random pipe heights with consistent gaps
- **Collision Detection**: Precise collision system
- **Score System**: Points awarded for passing through pipes
- **Local Storage**: Best score persistence across sessions

## Contributing

Feel free to contribute to this project by:
1. Forking the repository
2. Creating a feature branch
3. Making your changes
4. Submitting a pull request

## License

This project is open source and available under the [MIT License](LICENSE).

## Acknowledgments

- Inspired by the original Flappy Bird game
- Built with modern web technologies for educational purposes