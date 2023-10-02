<!-- src/MovieList.svelte -->
<script>
  let movies = [];
  let currentPage = 1;
  const totalPages = 10; // Nombre total de pages à afficher

  function formatFrenchDate(dateString) {
    const options = { year: "numeric", month: "long", day: "numeric" };
    return new Date(dateString).toLocaleDateString("fr-FR", options);
  } // Date format fr

  async function fetchMovies(page) {
    try {
      const apiKey = "536d240bd0d3bccc9480b1e8255bd5dd"; // clé API TMDb
      const response = await fetch(
        `https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&language=fr-FR&page=${page}`
      );

      if (response.ok) {
        const data = await response.json();
        movies = data.results.map((movie) => ({
          ...movie,
          release_date: formatFrenchDate(movie.release_date),
        }));
      } else {
        console.error("Erreur lors de la récupération des données.");
      }
    } catch (error) {
      console.error("Erreur lors de la récupération des données.", error);
    }
  }

  function nextPage() {
    if (currentPage < totalPages) {
      currentPage += 1;
      fetchMovies(currentPage);
    }
  }

  function previousPage() {
    if (currentPage > 1) {
      currentPage -= 1;
      fetchMovies(currentPage);
    }
  }

  fetchMovies(currentPage);
</script>

<h1>Films populaires (Page {currentPage} de {totalPages})</h1>

<section>
  <div class="container">
    {#each movies as movie (movie.id)}
      <div class="movie">
        <h2>{movie.title}</h2>
        <img
          src={`https://image.tmdb.org/t/p/w500${movie.poster_path}`}
          alt={`Affiche de ${movie.title}`}
        />
        <p>Sortie le : {movie.release_date}</p>
      </div>
    {/each}
  </div>
  <div class="page-buttons">
    <button on:click={previousPage} disabled={currentPage === 1}
      >Page précédente</button
    >
    <button on:click={nextPage} disabled={currentPage === totalPages}
      >Page suivante</button
    >
  </div>
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
  }

  .container {
    width: 95vw;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-around;
  }

  .movie {
    width: 20vw;
    display: flex;
    flex-direction: column;
    align-items: center;
    margin: 2% 0;
    background: rgb(255, 255, 255);
    background: linear-gradient(
      0deg,
      rgba(255, 255, 255, 1) 18%,
      rgba(73, 223, 255, 1) 49%,
      rgba(0, 44, 255, 1) 100%
    );
    box-shadow: gray 5px 5px 10px;
  }

  h2 {
    width: 18vw;
    height: 5vh;
    margin: 5%;
  }

  img {
    width: 18vw;
    height: 50vh;
  }

  p {
    width: 18vw;
  }
  .page-buttons {
    display: flex;
    flex-direction: row;
    justify-content: space-around;
  }

  button {
    width: 5vw;
    border: none;
    box-shadow: gray 5px 5px 10px;
  }
</style>
