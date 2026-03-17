---
sidebar_position: 2
---

# Uso: `@lib/api/fetcher`

`fetcher` (también conocido como "lasaña") es el sistema que usamos para fetchear. Se divide en dos partes:
- `usePassiveFetcher`, para obtener datos que se cargan con el componente y se actualizan en background. Por ejemplo,
  para obtener los datos de una tienda, generalmente con GETs.
- `useActiveFetcher`, para realizar peticiones activadas manualmente, como POSTs para crear y PUTs para crear.

Se *debe* usar la apropiada para el caso. *No se aceptarán PRs que usen otras herramientas para fetchear*.
A posterior se muestran 3 ejemplos: un GET automático que carga con un componente, un POST con JSON, y un PUT usando
FormData.

## `usePassiveFetcher` — Fetch automático (GET)

Para obtener datos que se cargan al montar el componente y se actualizan en background:

```tsx
const { data, isPending, isError } = usePassiveFetcher<Product[]>({
  url: 'products/',
});
```

- `data` es `undefined` mientras carga.
- `enabled: false` desactiva el fetch hasta que se cambie a `true`.

```tsx
const { data } = usePassiveFetcher<User>({
  url: `users/${id}/`,
  enabled: !!id,
});
```

---

## `useActiveFetcher` — POST con JSON

Para enviar datos con efecto secundario (crear, autenticar, etc.):

```tsx
const { fetch, isPending, isError } = useActiveFetcher<Order>({
  url: 'orders/',
  method: 'POST',
  onSuccess: (order) => router.push(`/orders/${order.id}`),
  onError: (err) => console.error(err),
});

// Al enviar el formulario:
<button onClick=() => {
  fetch({ body: { productId: 42, quantity: 3 } });
}>
```

- El `body` se serializa como JSON automáticamente.
- Se puede sobreescribir `url` y `method` al llamar `fetch(...)`.

---

## `useActiveFetcher` — PUT con imagen (FormData)

Para subir archivos, usa `formPayload` en lugar de `body`:

```tsx
const { fetch, isPending } = useActiveFetcher<User>({
  url: 'users/me/',
  method: 'PUT',
});

// Al seleccionar un archivo:
const handleSubmit = async (name: string, avatar: File) => {
  await fetch({
    formPayload: {
      name,
      avatar, // Blob/File se añade al FormData directamente
    },
  });
};
```

- Los campos `undefined` en `formPayload` se omiten automáticamente.
- No combines `body` y `formPayload`; si hay `formPayload`, tiene prioridad.
