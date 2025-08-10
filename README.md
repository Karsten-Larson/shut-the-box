# Shut The Box

This is a web-based implementation of the classic dice game "Shut The Box", built with Svelte 5 and Tailwind CSS.

## Gameplay

The objective of the game is to close all the numbered tiles on the board, or to finish with the lowest score. The game is played by rolling two dice and then selecting one or more of the available numbers that sum to the total of the dice roll. The selected numbers are then "shut" or unavailable for the rest of the game. The game ends when no combination of the remaining numbers can be used to match the dice roll. The player's score is the sum of the numbers that are still open.

For a more detailed explanation of the rules, you can refer to the [Wikipedia article on Shut The Box](https://en.wikipedia.org/wiki/Shut_the_box).

## Tech Stack

- **Svelte 5:** The game is built using the latest version of Svelte, which provides a simple and efficient way to build reactive user interfaces.
- **Tailwind CSS:** The styling is done using Tailwind CSS, a utility-first CSS framework that allows for rapid development of custom designs.
- **TypeScript:** The project is written in TypeScript, which adds static typing to JavaScript, improving code quality and maintainability.
- **Vite:** The development server and build process are handled by Vite, a fast and modern web development build tool.

## Development

This project was developed with the assistance of the Gemini CLI, using the `gemini-2.5-pro` model. The development process involved a conversation with the AI, where it was asked to implement the game logic, add features like confetti and a modal, and make the game responsive.

### Running the project

To run the project locally, you will need to have Node.js and pnpm installed. Then, follow these steps:

1.  Clone the repository.
2.  Install the dependencies with `pnpm install`.
3.  Start the development server with `pnpm run dev`.
4.  Open your browser to `http://localhost:5173`.

## Copyright

"Shut The Box" is a traditional pub game. While the exact origin and copyright status are unclear, it is widely considered to be in the public domain. This implementation is for educational and entertainment purposes only.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
