<script lang="ts">
  import type { Movie } from '$lib/types';

  // Props con Svelte 5: sistema de tipos explícito y callbacks en lugar de eventos
  let { 
    movie,
    showActions = true,
    ondelete,
    onedit,
    onrate
  }: {
    movie: Movie;
    showActions?: boolean;
    ondelete?: (id: string) => void;
    onedit?: (movie: Movie) => void;
    onrate?: (movie: Movie, rating: number) => void;
  } = $props();

  const stars = [1, 2, 3, 4, 5];
  let currentRating = $derived(movie.rating ?? 0);

  // Handlers: ejecutan callbacks del padre directamente
  function handleDelete() {
    ondelete?.(movie.id);
  }

  function handleEdit() {
    onedit?.(movie);
  }

  function handleRate(rating: number) {
    onrate?.(movie, rating);
  }
</script>

<!-- Componente reutilizable: tarjeta para mostrar información de una película -->
<article class="flex flex-col overflow-hidden rounded-lg border border-slate-200 bg-white shadow-sm">
  {#if movie.posterUrl}
    <div class="flex h-48 items-center justify-center bg-slate-100">
      <img
        alt={`Póster de ${movie.title}`}
        class="max-h-full max-w-full object-contain"
        src={movie.posterUrl}
        loading="lazy"
      />
    </div>
  {/if}

  <div class="flex flex-1 flex-col gap-3 p-4">
    <header>
      <h3 class="text-lg font-semibold text-slate-900">{movie.title}</h3>
      <p class="text-sm text-slate-600">Dirigida por {movie.director}</p>
    </header>

    <div class="mt-auto text-sm text-slate-500">
      {#if movie.year}
        <span>Año: {movie.year}</span>
      {/if}
    </div>

    <div class="flex items-center gap-1" aria-label={`Rating actual: ${currentRating} de 5`}>
      {#each stars as star}
        <button
          type="button"
          class={`text-xl leading-none transition hover:scale-110 ${
            star <= currentRating ? 'text-amber-500' : 'text-slate-300 hover:text-amber-400'
          }`}
          aria-label={`Calificar con ${star} de 5`}
          title={`Calificar con ${star} de 5`}
          onclick={() => handleRate(star)}
        >
          {star <= currentRating ? '★' : '☆'}
        </button>
      {/each}
    </div>

    {#if showActions}
      <div class="mt-3 flex flex-col gap-2 sm:flex-row">
        <button
          type="button"
          class="w-full rounded border border-slate-300 px-3 py-2 text-slate-700 transition hover:bg-slate-50"
          onclick={handleEdit}>
          Editar
        </button>
        <button
          type="button"
          class="w-full rounded border border-red-500 px-3 py-2 text-red-600 transition hover:bg-red-50"
          onclick={handleDelete}>
          Eliminar
        </button>
      </div>
    {/if}
  </div>
</article>
