---
title: Adding Search
order: 7
---

# Adding a search feature to our API

Let's consider adding a `search` feature to our API to satisfy the
`search / filter` in our UI.

We could add another endpoint such as `GET /api/Restaurants/search`, but we
already have an endpoint that represents what we want: _the list of
restaurants_, we just want a **variant** of that list.

In this case, we can use the `query parameter` feature of HTTP to our advantage,
and it is specifically what this feature is meant for.

We will update the definition of the `GetRestaurants` method as such:

```csharp
[HttpGet]
public async Task<ActionResult<IEnumerable<Restaurant>>> GetRestaurants(string filter)
```

The `string filter` will be populated with the **value** of a query parameter
named `filter` if present, otherwise the variable `filter` will be `null`.

We can use this to our advantage in the implementation. If the `filter` variable
is `null` we simply return the list of all restaurants from the context.
Otherwise, we add a `Where` method to only include the `restaurant` where the
`Name` property contains the phrase in the `filter` variable.

```csharp
if (filter == null)
{
    return await _context.Restaurants.ToListAsync();
}
else
{
    return await _context.Restaurants.Where(restaurant => restaurant.Name.ToLower().Contains(filter.ToLower())).ToListAsync();
}
```

## Updating the UI to allow for filtering

First, let's update the `NavBar` to contain a state for the filter text.

```javascript
const [filterText, setFilterText] = useState('')
```

Then we will update the `<input>` tag

```jsx
<input
  type="text"
  placeholder="Search..."
  value={filterText}
  onChange={event =>
    function () {
      setFilterText(event.target.value)
    }
  }
/>
```

## Update the useEffect to take the filterText into consideration

```javascript
useEffect(() => {
  async function loadRestaurants() {
    const url =
      filterText.length === 0
        ? `/api/Restaurants`
        : `/api/Restaurants?filter=${filterText}`

    const response = await fetch(url)
    const json = await response.json()

    setRestaurants(json)
  }

  loadRestaurants()
}, [filterText])
```

## Files Updated

<GithubCommitViewer repo="gstark/TacoTuesday" commit="831c8db96d0a96f1b02e73a324478013edca4279"/>
