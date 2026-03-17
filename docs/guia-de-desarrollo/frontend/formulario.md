---
sidebar_position: 3
---

# Formularios

React Hook Form es una librería que automáticamente valida las entradas de los formularios, dando errores y evitando
poner estado.

Sin RHF, manejar formularios en React requiere mucho código repetitivo:

```tsx
// Sin RHF — todo manual
const [email, setEmail] = useState('');
const [password, setPassword] = useState('');
const [emailError, setEmailError] = useState('');
// ...validar a mano en cada onChange o onSubmit
```

Con RHF:

### 1. Define el schema con Zod

```tsx
import { z } from 'zod';

const loginSchema = z.object({
  email: z.string().email('Email inválido'),
  password: z.string().min(1, 'Contraseña requerida'),
});

// TypeScript gratis — equivale a: { email: string; password: string }
type LoginValues = z.infer<typeof loginSchema>;
```

### 2. Inicializa el formulario

```tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';

const {
  register,           // conecta <input> al formulario
  handleSubmit,       // envuelve tu onSubmit con validación
  formState: {
    errors,           // errores por campo
    isSubmitting,     // true mientras el submit está en curso
  },
} = useForm<LoginValues>({
  resolver: zodResolver(loginSchema),  // conecta Zod
});
```

### 3. Conecta los inputs

```tsx
<input
  type="email"
  aria-invalid={!!errors.email}
  {...register('email')}   // registra el campo: name, onChange, onBlur, ref
/>
{errors.email && <p>{errors.email.message}</p>}
```

### 4. Maneja el submit

```tsx
async function onSubmit(data: LoginValues) {
  // data ya está validado y tipado
  await login.fetch({ body: data });
}

<form onSubmit={handleSubmit(onSubmit)}>
  {/* handleSubmit ejecuta onSubmit SOLO si la validación pasa */}
```

---

## Imágenes con `ImageUpload` + RHF

El componente `ImageUpload` (`components/dondeSiempre/ImageUpload.tsx`) recibe un callback `onChange(file: File | null)`. Como no es un `<input>` nativo, no se puede usar `register` directamente — hay que usar **`Controller`**.

### Con imagen obligatoria

```tsx
import { useForm, Controller } from 'react-hook-form';
import { z } from 'zod';
import ImageUpload from '@/components/dondeSiempre/ImageUpload';

const schema = z.object({
  name: z.string().min(1, 'Nombre requerido'),
  // File no es serializable, usamos z.instanceof
  image: z.instanceof(File, { message: 'La imagen es obligatoria' }),
});

type FormValues = z.infer<typeof schema>;

export function MyForm() {
  const {
    register,
    control,          // necesario para Controller
    handleSubmit,
    formState: { errors },
  } = useForm<FormValues>({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input {...register('name')} />
      {errors.name && <p>{errors.name.message}</p>}

      <Controller
        name="image"
        control={control}
        render={({ field }) => (
          <ImageUpload
            onChange={(file) => field.onChange(file)}  // conecta al formulario
          />
        )}
      />
      {errors.image && <p>{errors.image.message}</p>}

      <button type="submit">Enviar</button>
    </form>
  );
}
```

### Con imagen opcional

```tsx
const schema = z.object({
  name: z.string().min(1, 'Nombre requerido'),
  image: z.instanceof(File).nullable().optional(),
  // nullable() → acepta null (cuando el usuario no sube nada)
  // optional() → acepta undefined (si el campo no aparece)
});
```

Con imagen opcional no necesitas `Controller` si prefieres `useState` como hacen `PromotionForm.tsx` y `create-outfit/page.tsx`:

```tsx
const [imageFile, setImageFile] = useState<File | null>(null);

// En el submit, imageFile puede ser null — lo incluyes manualmente en el DTO
<ImageUpload onChange={setImageFile} />
```

Úsalo con `Controller` cuando quieras que RHF controle la validación de la imagen junto con el resto del formulario. Usa `useState` cuando la imagen sea opcional y no necesites validarla.

---

## Errores de API

RHF no sabe del backend, así que los errores de API se manejan con un `useState` aparte, como en `loginForm.tsx`:

```tsx
const [apiError, setApiError] = useState<string | null>(null);

async function onSubmit(data: FormValues) {
  setApiError(null);
  try {
    await login.fetch({ body: data });
  } catch (err) {
    if (err instanceof FetchError && err.response?.status === 403) {
      setApiError('Credenciales incorrectos.');
    } else {
      setApiError('Ha ocurrido un error.');
    }
  }
}

{apiError && <p className="text-xs text-destructive">{apiError}</p>}
```

