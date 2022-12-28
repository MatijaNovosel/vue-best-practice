<div align="center">
  <img src="https://user-images.githubusercontent.com/36193643/205978239-f29e0f55-a561-4b90-931c-066045662f9b.png" />
</div>

<h1 align=center>Vue/frontend best practices</h1>

S obzirom da na projektu rade više ljudi istovremeno, dolazimo do problema gdje se ne držimo istog standarda za pisanje koda na frontendu. Ovaj README služi tome i u njemu će biti razjašnjeno kojeg bi se standarda trebali držati.

## 📚 Sadržaj

Dodati sadržaj ovdje.

## ⚙️ Preduvjeti

Kako bi se moglo raditi na ovom projektu efektivno, potrebno je instalirati sljedeće:

- [Visual Studio Code](https://code.visualstudio.com/)
- Obavezni extensioni
  - [Auto Import](https://marketplace.visualstudio.com/items?itemName=steoates.autoimport)
  - [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
  - [Path Intellisense](https://marketplace.visualstudio.com/items?itemName=christian-kohler.path-intellisense)
  - [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)
  - [Sass](https://marketplace.visualstudio.com/items?itemName=Syler.sass-indented)
  - [TypeScript Vue Plugin (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.vscode-typescript-vue-plugin)
  - [VS Code .\*proj](https://marketplace.visualstudio.com/items?itemName=jRichardeau.vscode-vsproj)
  - [Vue Language Features (Volar)](https://marketplace.visualstudio.com/items?itemName=Vue.volar)
- Preporučeni extensioni
  - [Color Highlight](https://marketplace.visualstudio.com/items?itemName=naumovs.color-highlight)
  - [Error Lens](https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens)
  - [TODO Highlight](https://marketplace.visualstudio.com/items?itemName=wayou.vscode-todo-highlight)

Za automatsko formatiranje i uklanjanje bespotrebnih importova potrebno je unutar `settings.json` unutar Visual Studio Coda postaviti sljedeće:

```
"editor.formatOnSave": true,
"editor.codeActionsOnSave": {
  "source.organizeImports": true
},
```

## 🌍 Generalno

- Imena datoteka Viewova i komponenta se pišu u PascalCasu, modeli i typescript/javascript datoteke u camelCasu
- CSS klase, id-evi, propovi, emitovi i slično pišu se isključivo u kebab-casu

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```html
<imeNekeKomponente class="nekaKlasa klasa2" @nekiEmit="funkcija" nekiProp="xyz" />
```

</td>
<td>

```html
<ime-neke-komponente class="neka-klasa klasa-2" @neki-emit="funkcija" neki-prop="xyz" />
```

</td>
</tr>
</table>

- Sav CSS koji nije `scoped` pisati unutar globalnog css-a

## 📦 Propovi

#### Vue 2

Definiraju se unutar `props` propertya od `defineComponent` funkcije.

```typescript
defineComponent({
  props: {
    name: {
      type: String,
      required: true,
      default: "No name"
    }
  },
  setup(props) {
    return {};
  }
});
```

#### Vue 3

Definiraju se unutar `defineProps` funkcije koja je dostupna top-level, koristi se navedena sintaksa jer je pristup koji koristi generiku malo [preopširan](https://vuejs.org/guide/typescript/composition-api.html#typing-component-props) i treba više boilerplata kako bi se definirale defaultne vrijednosti.

```typescript
// Lijeva strana se ne mora pisati ako se propovi ne koriste u <script>
const props = defineProps({
  name: {
    type: String,
    required: true,
    default: "No name"
  }
});
```

Propove je uvijek potrebno pisati u camelCasu kad ih se definira unutar komponente, a u kebab-casu kad se navode na sučelju.

```typescript
defineProps({
  personName: {
    type: String,
    default: ""
  }
});
```

```html
<person-details person-name="Branko" />
```

**Nadalje se neće odavajati sintaksa po Vue verzijama jer je definiranje propova identično.**

### Tipiziranje

Nije potrebno pisati dodatni `interface` za propove jer se svi tipovi već znaju prilikom njihovog definiranja i intellisense ih može prepoznati.

Primitivni tipovi koji postoje su: `Number`, `String`, `Boolean`, dok se za definiranje kompliciranijih tipova (`Function`,`Object`,`Array`,`Symbol`,`Date`) koristi wrappanje s `PropType<T>` u kombinaciji s odgovarajućim tipom.

Po defaultu svaki prop kod kojeg nije naveden `required` na `true` biti će spojen s tipom `undefined`, potrebno je prikladno handlati takvu situaciju iako se inače preporuča što više koristiti neku defaultnu vrijednost.

#### Object

Ako je potrebno da `Object` tip propa bude nullabilan onda obavezno koristiti `|` operator i defaultno stanje propa na `null`.

```typescript
interface Person {
  name: string;
  surname: string;
}

defineProps({
  person: {
    type: Object as PropType<Person | null>,
    default: null
  }
});
```

#### Array

```typescript
interface Person {
  name: string;
  surname: string;
}

defineProps({
  people: {
    type: Array as PropType<Person[]>
  }
});
```

## 🎉 Emitovi

#### Vue 2

Definiraju se unutar `emits` propertya od `defineComponent` funkcije i pozivaju s `$emit` funkcijom na sučelju i s `emit` funkcijom unutar `<script>`. Obavezno se pišu u kebab-casu.

```typescript
defineComponent({
  emits: ["show-details"],
  setup(props, context) {
    context.emit("show-details");
    return {};
  }
});
```

#### Vue 3

Definiraju se unutar `defineEmits` funkcije, a pozivaju povratnom vrijednošću unutar `<script>` po potrebi ili s `$emit` na sučelju.

```typescript
// Lijeva strana se ne mora pisati ako se emitovi ne koriste u <script>
const emit = defineEmits(["show-details"]);
emit("show-details", 1);
```

Primjer korištenja na komponenti:

```html
<neka-komponenta @show-details="showDetails" />
```

```typescript
const showDetails = (id: number) => {
  console.log(id);
};
```

## ✅ Validacija

Za validaciju koristi se component-based [vee-validate](https://vee-validate.logaretm.com/v3/guide/basics.html) library.

U tu svrhu definirane su dvije globalne komponente: `validation-observer` i `validation-provider`. Prvo se koristi kao wrapper (forma) niza polja, tj. `validation-provider`-a, dok se drugo koristi kao
