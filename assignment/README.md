# `useEffect` Pokemon Assignment

## Overview
Build a React component that allows users to search for a Pokémon by name, fetch data from the PokéAPI, and display its sprite. Clicking the sprite should toggle between the front and back image.

### Exploring the PokeAPI

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

## Requirements

- Provide a search input and submit button for the user to enter a Pokémon name.  
- When a search is submitted, fetch that Pokémon’s data from the PokéAPI:  
  `https://pokeapi.co/api/v2/pokemon/{name}`  
- Display the Pokémon’s name and sprite.  
- Clicking the sprite toggles between the front and back image.   
- The component should update the displayed Pokémon whenever a new search is submitted or the user toggles the sprite.

---

### Bonus Challenges

- Add additional details about the Pokémon, such as its type, abilities, and stats.
- Add a message to inform users when the Pokémon is not found.
- Set the pokemon to always show its front when you search for it. Currently, if you flip a pokemon to its back and then search for a new pokemon, the new pokemon will show its back. **This will take some rewriting of our solution**, since you'll want to make a change to the `isFront` state _not_ make a new API call but to instead make which image to show dependent on the `isFront` state. You'll thus need to save both images in some way, or save the whole pokemon you get back from the server.
- Style the app to make it visually appealing.
