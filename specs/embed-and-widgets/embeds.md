# Embed support

To place an embed in the page, the website owner includes a placeholder `div` element. For example, a weather embed could look like this:

```html
<!--
Use an <instagram-post></instagram-post> HTML element to tell Zaraz where to put the embedded content.

Add a post-url attribute that contains a URL to the post you want to embed,
and optionally add captions="true" to determine whether to include post captions.
-->

<instagram-post
  post-url="https://www.instagram.com/p/C3Sk6d2MTjI/"
  captions="true"
></instagram-post>
  
```

Inside the External Component, the embed will be defined like in this example:

```js
manager.registerEmbed(
  "weather-example",
  async ({ parameters }: { parameters: { [k: string]: string } }) => {
    const location = parameters["location"];
    const embed = await manager.useCache("weather-" + location, async () => {
      try {
        const response = await manager.fetch(
          `https://wttr.in/${location}?format=j1`
        );
        const data = await response.json();
        const [summary] = data.current_condition;
        const { temp_C } = summary;
        return `<p>Temperature in ${location} is: ${temp_C} &#8451;</p>`;
      } catch (error) {
        console.error("error fetching weather for embed:", error);
        return `<p>Error loading weather for ${location}: ${error}</p>`;
      }
    });
    return embed;
  }
);
```

In the above example, the tool defines an embed called `weather-example`. It makes a request to a remote API, caches it, and then renders a simple html, using the data from the fetch response.
