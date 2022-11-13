## Setup

1. Clone the repo: `git clone https://github.com/hejtful/slate.git`
2. Switch to the fill-in feature branch: `git checkout feat/fill-in-inline`
3. Install the packages: `yarn install`
4. Build Slate: `yarn build`
5. Start the watcher: `yarn start`
6. Open the "Inlines" example: `yarn open` (alternatively just open `http://localhost:3000/examples/inlines` in your browser)

## Approach

To save time on project setup, I decided to start the PoC by cloning the Slate repo and extend the existing "Inlines" example, to add the fill-in functionality as well.

To be consistent with the existing code and UI, and therefore help the reviewer understand my changes, I kept the UI and code styles from the existing example.

It is possible, using a toolbar button (pencil on a line) or a keyboard shortcut (cmd+e on MacOS / ctrl+e on Windows), to add a fill-in, remove a fill-in, wrap existing text in a fill-in and edit the fill-in's correct answer.

All changes are saved to LocalStorage. The expected answer is a separate `expectedAnswer` property of the fill-in element's object.

## Alternative UX idea

The current implementation is the following:

- When selection is **collapsed**, a prompt will be shown to the user, where the user can choose the correct answer.
- When selection is **expanded**, the selected text will be assumed as the correct answer.
- When a fill-in is selected, the `FillInComponent` will be removed and the correct answer will be merged with the text around it.
- Changing the text inside of `FillInComponent` will change the value of the `correctAnswer` field.

However, a more rigid implementation might be desired:

- When selection is **collapsed**, a `FillInDialog` will open, asking the user for the correct answer of the new fill-in.
- When selection is **expanded**, a `FillInDialog` will open, with the correct answer already pre-filled with the selected text.
- For FillInComponent `span` element, `contenteditable` is disabled.
- When a fill-in is selected, a `FillInDialog` will open, which allows the user to edit the correct answer, or to remove the `FillInComponent` and merge its text with the text around it.

## Necessary steps before production

- Check if the format in which the whole fill-in element is saved, and especially the expected answer value, is usable in the broader Serlo system.
- Make sure that all keyboard keys and combinations of keys work as expected while a fill-in element is selected. For example, currently, pressing the enter key will split the selected fill-in element in two. It's probably preferred to move the selection to just after that fill-in element (so it seems like the enter key confirms the correct answer value).
- Adjust the styling of the button, so it fits in with the Serlo UI styleguide. Add a tooltip to the button, which explains what the button does and also displays its keyboard shortcut.
- Make sure that the keyboard shortcut is intuitive and easy to remember for existing Serlo contributors.
- Adjust the styling of the fill-in, so it wits in with the Serlo UI styleguide.
- Allow adding multiple possible answers. Adjust the UI of the fill-in in the editor, to make it clear to the contributor that multiple answers are possible and what those answers are. It might be as simple as showing the answers together like "answer1 / answer2 / answer3". Or, it might be something more complex like showing only "answer1" with an indicator that there are multiple answers, while a hover or a click on the indicator displays the rest of the answers in a list.
- Allow defining a margin of error for typos etc, for example: The correct answer is "Bibliography". Student types in "Bibliorgaphy". If the margin of error is 10%, it is accepted as a correct answer (and the typo is potentially automatically corrected in the student view, while showing the student a visual hint that a typo was present). Margin of error could be defined for the whole exercise (for example, if only numbers are expected in the fill-ins, it would probably be 0), and then optionally for individual fill-ins to override the exercise-wide margin of error.
- Possibly, not all of these steps are necessary and would be discussed by the team. I would also rely on team feedback for more necessary steps that I missed.
