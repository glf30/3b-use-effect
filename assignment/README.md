# `useEffect` Pokemon Assignment

### Step 1: Set Up the React Project

- Open the terminal to the `assignment` directory--the simplest way to do so is to right-click on the `assignment` folder in VS Code and select "Open in Integrated Terminal".

- In the terminal, type `npm create vite .` and hit enter/return. The `.` is important--this will create a new Vite project in the current directory.

- It will warn you that there are files here currently. Use the arrow keys and Enter/Return to select "Ignore files and continue". This allows us to keep our readme and any data/assets files we have in our new project folder.

- Choose React and then JavaScript from the following menus, using arrow keys and Enter/Return.

- Install dependencies by entering `npm install` in the terminal.

- Run the app by typing `npm run dev` in the terminal. This will provide a clickable link to open the app in your default browser, or you can navigate to the localhost URL in your browser.

### Step 2: Exploring the PokeAPI

Spend some time exploring the PokeAPI. Familiarize yourself with how to make requests and the structure of the data returned.

##### Access the API:

1. Visit the PokeAPI documentation: [https://pokeapi.co/docs/v2](https://pokeapi.co/docs/v2)
2. Use your browser or a tool like Postman to make a GET request to `https://pokeapi.co/api/v2/pokemon/{name or id}`.
3. Replace `{name or id}` with a Pokémon's name (e.g., `bulbasaur`) or ID (e.g., `1`).

##### Review the JSON Response:

Example JSON response for Bulbasaur:

```json
{
  "name": "bulbasaur",
  "id": 1,
  "sprites": {
    "front_default": "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/1.png",
    "back_default": "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/back/1.png"
  },
  "types": [
    {
      "slot": 1,
      "type": {
        "name": "grass",
        "url": "https://pokeapi.co/api/v2/type/12/"
      }
    },
    {
      "slot": 2,
      "type": {
        "name": "poison",
        "url": "https://pokeapi.co/api/v2/type/4/"
      }
    }
  ]
}
```

Focus on fields like name, id, sprites, and types.

Once you've reviewed and understood the structure of the JSON response, you're ready to move on to the next step.

### Step 3: Designing the React App

This step is broken down into detailed sub-steps for each component of the app. Follow these steps carefully to build the app incrementally.

##### Search Bar

Create an input field where users can enter the name of a Pokémon and a button to trigger the search.

1. **Add State for Search Query:**

   - Create a state variable to manage the search query input.
   - Example:

```jsx
const [searchQuery, setSearchQuery] = useState("");
```

2. **Handle Input Changes:**

   - Add an `onChange` event to update the state whenever the user types in the input field.
   - Example:

```jsx
<input
  type="text"
  value={searchQuery}
  onChange={(e) => setSearchQuery(e.target.value)}
  placeholder="Enter Pokémon name"
/>
```

3. **Trigger the Search:**

   - Create a function to handle form submission and set another state for the Pokémon name.
   - Example:

```jsx
const handleSearch = (e) => {
  e.preventDefault();
  setPokemonName(searchQuery.toLowerCase());
};
```

##### Display Area

Create an area to display the fetched Pokémon's image. Use state to manage the displayed image and whether to show the front or back image.

1. **Add State for Pokémon Image:**

   - Create a state variable to manage the Pokémon image URL.
   - Example:

```jsx
const [pokemonImage, setPokemonImage] = useState("");
```

2. **Manage Front/Back Image Toggle:**

   - Create a state variable to manage whether the front or back image is displayed.
   - Example:

```jsx
const [isFront, setIsFront] = useState(true);
```

##### Toggle Functionality

Implement functionality to toggle between the front and back images when the image is clicked. Use `useEffect` to fetch the image based on the current state.

1. **Create an Effect Hook:**

   - Use `useEffect` to make an API request based on the `isFront` and `pokemonName` states.
   - The `useEffect` should be dependent on a state variable that updates when the user submits the Pokémon name.
   - Example:

```jsx
useEffect(() => {
  const fetchPokemonImage = async () => {
    const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemonName}`);
    const pokemon = await response.json();
    setPokemonImage(pokemon.sprites[isFront ? "front_default" : "back_default"]);
  };

  if (pokemonName) {
    fetchPokemonImage();
  }
}, [isFront, pokemonName]);
```

2. **Handle Image Click:**

   - Add an `onClick` event to the image to toggle the `isFront` state.
   - Example:

```jsx
<div onClick={() => setIsFront((prevIsFront) => !prevIsFront)}>
  <img src={pokemonImage} alt={pokemonName} />
</div>
```

##### Putting It All Together

Combine the components (Search Bar, Display Area, and Toggle Functionality) to create a cohesive user experience.

1. **Structure the Component:**

   - Create a main component to house the search bar, display area, and toggle functionality.
   - Example structure:

```jsx
return (
  <div>
    <form onSubmit={handleSearch}>
      <input
        type="text"
        value={searchQuery}
        onChange={(e) => setSearchQuery(e.target.value)}
        placeholder="Enter Pokémon name"
      />
      <button type="submit">Search</button>
    </form>
    {pokemonImage && (
      <div onClick={() => setIsFront((prevIsFront) => !prevIsFront)}>
        <img src={pokemonImage} alt={pokemonName} />
      </div>
    )}
  </div>
);
```

2. **Test Your App:**

   - Test your app thoroughly to ensure it works as expected.
   - Handle various edge cases, such as invalid Pokémon names or network errors.

### Bonus Challenges

- Add additional details about the Pokémon, such as its type, abilities, and stats.
- Add a message to inform users when the Pokémon is not found.
- Set the pokemon to always show its front when you search for it. Currently, if you flip a pokemon to its back and then search for a new pokemon, the new pokemon will show its back. **This will take some rewriting of our solution**, since you'll want to make a change to the `isFront` state _not_ make a new API call but to instead make which image to show dependent on the `isFront` state. You'll thus need to save both images in some way, or save the whole pokemon you get back from the server.
- Style the app to make it visually appealing.
