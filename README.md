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

Za automatsko formatiranje i uklanjanje bespotrebnih importova potrebno je unutar `settings.json` Visual Studio Coda postaviti sljedeće:

```json
"editor.formatOnSave": true,
"editor.codeActionsOnSave": {
  "source.organizeImports": true
},
```

## 🚀 Uvod

Struktura projekta podijeljena je na tri dijela: `admin`, `user` i `shared`.

### Admin & user

Admin i user dio sastoje se od specifične strukture direktorija:

- `components` - sadrži .vue komponente koje mogu biti unutar posebnih direktorija ovisno o situaciji npr. folder timeline s komponentama timeline-list.vue i timeline-item.vue
- `layouts` – sadrži .vue datoteke kojima se opisuje struktura sučelja, naročito navigacijske komponente kao i router view
- `modules` – niz direktorija kojima se opisuju dijelovi aplikacije, odvojeni po svojoj svrsi npr. module lines se bavi dijelom aplikacije zaduženim za informacije i - funkcionalnosti autobusnih linija. Svaki modul sastoji se od slične strukture direktorija izuzev direktorija layouts.
- `routes` – popis ruta aplikacije, stvoreno od spajanja ruta modula zajedno

### Shared

Unutar shared direktorija nalaze se datoteke koje su potrebne na globalnoj razini poput komponenti koje su zajedničke za admin i user dio, prijevodi i slično ili dodatci aplikacije poput routera ili stora. U ovom slučaju struktura navedenog direktorija je kao što slijedi:

- `assets` – css datoteke i ostale datoteke poput slika potrebne frontendu
- `components` – komponente koje su potrebne na globalnoj razini
- `constants` – enumeracije i konstantne vrijednosti
- `hooks` – composable funkcije
- `infrastructure` – Nswag klijent za pravljenje requestova prema backendu
- `interfaces` – definicije interfacova za servise kojime će se praviti requestovi prema backendu koristeći IoC container Inversify
- `layouts` – layouti za dijelove aplikacije koji su neovisni o user i admin dijelu poput autentifikacije
- `models` – definicije modela podataka za razne dijelove aplikacije i servise
- `plugins` – dodatci aplikaciji poput prijevoda, axiosa itd.
- `router` – definicije zajedničkih ruta aplikacije
- `services` – definicije realiziranih interfacova koji su opisani u interfaces direktoriju
- `store` – Vuex store za dijeljenje podataka duž aplikacije
- `translations` – prijevodi na engleskom i hrvatskom jeziku
- `validators` – dodatne definicije validacija za forme i polja

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
<imeNekeKomponente
  class="nekaKlasa klasa2"
  @nekiEmit="funkcija"
  nekiProp="xyz"
/>
```

</td>
<td>

```html
<ime-neke-komponente
  class="neka-klasa klasa-2"
  @neki-emit="funkcija"
  neki-prop="xyz"
/>
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

## 🏳️ Prijevodi

Za prijevode koristi se [i18n](https://kazupon.github.io/vue-i18n/) package.

`en.ts` se koristi za engleske i `hr.ts` za hrvatske prijevode, najbolje ih je organizirati po screenovima ili po nekim generalnim pojmovima.

## ✅ Validacija

Za validaciju koristi se component-based [vee-validate](https://vee-validate.logaretm.com/v3/guide/basics.html) library.

U tu svrhu definirane su dvije globalne komponente: `validation-observer` i `validation-provider`. Prvo se koristi kao wrapper (forma) niza polja, tj. `validation-provider`-a, dok se drugo koristi kao jedinica za validaciju tj. polje.

Svaki skup polja mora biti unutar točno određene forme, primjerice:

```html
<validation-observer ref="formRef" slim v-slot="{ handleSubmit }">
  <v-form @submit.prevent="handleSubmit(submitFn)">
    <v-row>
      <v-col cols="12">
        <validation-provider
          vid="fieldVid"
          :name="$t('language')"
          rules="required"
          v-slot="{ errors, required }"
        >
          <v-select
            v-model="state.model"
            :items="[]"
            :error-messages="errors"
            hide-details="auto"
            item-text="text"
            item-value="value"
            label="Label"
          >
            <template #label>
              <required-icon v-show="required" />
              <span> Label </span>
            </template>
          </v-select>
        </validation-provider>
      </v-col>
      <v-col>
        <v-btn type="submit" />
      </v-col>
    </v-row>
  </v-form>
</validation-observer>
```

Svaka forma mora imati gumb s propom `type` postavljen na `submit`, **ne kombinirajući to s `@click` direktivom**.

### validation-observer

Kod `validation-observer`-a potrebno je napraviti `ref` kako bi se forma mogla resetirati naknadno, navesti prop `slim` radi manje natrpanog DOM-a i exposati `handleSubmit` funkciju pomoću `v-slot`-a kako bi se mogla izvršiti radnja asocirana sa slanjem te forme.

Ujedno prvo dijete unutar observera mora biti `v-form` s definiranom radnjom koja će se izvršiti.

Referenca na komponentu forme mora biti tipizirana:

```typescript
interface IValidationObserverForm {
  reset: () => void;
}

const formRef = ref<IValidationObserverForm | null>(null);
formRef.value?.reset();
```

Svaka referenca je potencijalno nullabilna pa se i taj slučaj mora očekivati.

### validation-provider

Svaki `validation-provider` mora imati definiran `vid`, `name`, `rules` i exposati ostale propertye po potrebi preko `v-slot`-a.
