
# What is LINQ ?
- LINQ means Language Integrated Query.
- it helps Find, Sort and Filter Data.
- No long loops - just short, simple commands.

```csharp
var games = new List<Game> 
{
new Game { Title = "Elden Ring", Genre = "Soul", ReleaseYear = 2023, Rating = 5 },
new Game { Title = "GTA", Genre = "Action", ReleaseYear = 2026, Rating = 4 },
new Game { Title = "Spiderman", Genre = "Comic", ReleaseYear = 2014, Rating = 4 },
new Game { Title = "Mario", Genre = "2D", ReleaseYear = 1994, Rating = 2 },
};

var gamesTitle = games.Select(game => game.Title);

var gamesTitle = games.Select(game => game.Genre).Distinct();

var gamesTitleGener = games.Select(game => new { game.Title, game.Genre } );

var actionGame = games.Where(game => game.Genre == "Action");

bool isGameExist = games.Any(game => game.ReleaseYear > 2000); 

var sortedGames = games.OrderBy(game => game.ReleaseYear ?? int.MaxValue);

var averageRating = games.Average(g => g.Rating); // Max, Min

var firstFindGame = games.First(g1 => g1.Rating == games.Max(g2 => g2.Rating));

var firstCheapestGame = games.OrderBy(g1 => g1.Price).First();

var groupByGener = game.GroupBy(g => g.Genre); // Action => { }, 2D => {}

var groupWhereGame = games.Where(g => g.Genre == "Action" && g.Rating < 3)
					.OrderByDescending(g => g.ReleaseYear)
					.Select(g => $"{g.Title} {g.Gener}");

var paginated = games.Skip(0).Take(3);  // Page 1
				games.Skip(3).Take(3); // Page 2

// Query

var adventureGame = from g in games
					where g.Genre == "Adventure"
					select g;
```