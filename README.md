<div align="center">
  <img src="https://user-images.githubusercontent.com/36193643/205978239-f29e0f55-a561-4b90-931c-066045662f9b.png" />
</div>

<h1 align=center>Vue/frontend best practices</h1>

S obzirom da na projektu rade više ljudi istovremeno, dolazimo do problema gdje se ne držimo istog standarda za pisanje koda na frontendu. Ovaj README služi tome i u njemu će biti razjašnjeno kojeg bi se standarda trebali držati.

Opisuju se ujedno i tehnički detalji i oni koji se tiču stiliziranja koda.

## Sadržaj

- [Preduvjeti](#preduvjeti)
- [Uvod](#uvod)
  - [Admin i user](#admin-i-user)
  - [Shared](#shared)
- [Generalno](#generalno)
- [Propovi](#propovi)
  - [Vue 2](#vue-2)
  - [Vue 3](#vue-3)
  * [Tipiziranje](#tipiziranje)
    - [Object](#object)
    - [Array](#array)
- [Emitovi](#emitovi)
  - [Vue 2](#vue-2-1)
  - [Vue 3](#vue-3-1)
- [Reaktivno stanje](#reaktivno-stanje)
- [Uvjetovani prikaz](#uvjetovani-prikaz)
- [Petlje](#petlje)
- [Slotovi](#slotovi)
- [Prijevodi](#prijevodi)
- [Validacija](#validacija)
  - [validation-observer](#validation-observer)
  - [validation-provider](#validation-provider)
  - [Greške kod validacije](#greške-kod-validacije)
- [Vuetify](#vuetify)
  - [Grid sistem](#grid-sistem)
  - [v-list](#v-list)
  - [v-data-table](#v-data-table)

## Preduvjeti

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

Pravila za formatiranje koda već su definirana unutar `.prettierrc` datoteke, a Prettier će ih sam interpretirati na automatskom formatiranju pa ne dolazi do razlike u pisanju koda kod različitih ljudi.

Preporučaju se ove postavke za Prettier:

```json
{
  "trailingComma": "none",
  "tabWidth": 2,
  "semi": true,
  "singleQuote": false,
  "printWidth": 80
}
```

## Uvod

Struktura projekta podijeljena je na tri dijela: `admin`, `user` i `shared`.

### Admin i user

Admin i user dio sastoje se od specifične strukture direktorija:

- `components` - sadrži .vue komponente koje mogu biti unutar posebnih direktorija ovisno o situaciji npr. folder timeline s komponentama `timeline-list.vue` i `timeline-item.vue`
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

## Generalno

- Imena datoteka Viewova, komponenta i modela se pišu u PascalCasu, imena typescript/javascript datoteke i datoteka modela u camelCasu
- Ako moguće, uvijek koristiti klase i izbjegavati inline stiliziranje
- Koristiti gotove **Vuetify** CSS klase ako postoje za određenu situaciju, ako ne onda napisati svoje u globalnoj css datoteci `site.css` ili pod kojim god imenom je deklarirano
- CSS klase, id-evi, propovi, emitovi i slično pišu se isključivo u kebab-casu
- Uvijek zatvarati elemente ako nemaju ničega u sebi
- Ako element ima nešto u sebi onda posložiti na smisleni način i odvojiti s `\n` kako sve nebi bilo u jednom redu
- Uvijek koristiti shorthand sintaksu za listenere, slotove i slično

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
interface nekiModel {
  name: string;
}
```

```html
<imeNekeKomponente
  v-on:click="klikHandler"
  class="nekaKlasa klasa2"
  @nekiEmit="funkcija"
  nekiProp="xyz"
  ><dijeteKomponenta></dijeteKomponenta
></imeNekeKomponente>
```

</td>
<td>

```typescript
interface NekiModel {
  name: string;
}
```

```html
<ime-neke-komponente
  @click="klikHandler"
  class="neka-klasa klasa-2"
  @neki-emit="funkcija"
  neki-prop="xyz"
>
  <dijete-komponenta />
</ime-neke-komponente>
```

</td>
</tr>
</table>

- Sav CSS koji nije `scoped` pisati unutar globalnog css-a
- Izbjegavati tip `any` ako je moguće
- Što više koristiti string literalse umjesto konkatenacije stringova

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
const name = firstName + " " + middleName + " " + lastName;
```

</td>
<td>

```typescript
const name = `${firstName} ${middleName} ${lastName}`;
```

</td>
</tr>
</table>

- Ako je potrebno napisati neku kratku funkciju ili helper, rađe je implementirati sam u `helpers.ts` nego instalirati posebni package za to jer postoje [ogromni problemi](https://qz.com/646467/how-one-programmer-broke-the-internet-by-deleting-a-tiny-piece-of-code) s 3rd party packagima
- Umjesto pisanja cijele funkcije s već poznatim ključnim riječima, pisati anonimne funkcije i spremati u varijable s obzirom da se `this` rijetko koristi

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
async function funkcija() {
  return await nesto();
}
```

</td>
<td>

```typescript
const funkcija = async () => {
  return await nesto();
};
```

</td>
</tr>
</table>

- Tipizirati povratne tipove funkcija i varijable, osim naravno u slučajevima gdje je evidentno koji je povratni tip iz intellisensa
- Koristiti `interface` za definicije modela rađe nego `type`, razlika objašnjena [ovdje](https://blog.devgenius.io/interfaces-vs-types-in-typescript-7567ffe390cd#:~:text=While%20type%20aliases%20and%20interfaces,be%20indirectly%20extended%20via%20intersections.)

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
export type Props = {
  name: string;
  count: number;
};
```

</td>
<td>

```typescript
export interface Props {
  name: string;
  count: number;
}
```

</td>
</tr>
</table>

- Ako se servis koristi na više mjesta unutar datoteke onda ga postaviti jednom na vrhu i pozivati funkcije preko varijable kojom je definiran, a ne pojedinačnim instanciranjem tog servisa i njegovih funkcija

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
await getService<IService>(Types.Service).getData();
await getService<IService>(Types.Service).sendData({ data: 1 });
```

</td>
<td>

```typescript
const service = getService<IService>(Types.Service);
await service.getData();
await service.sendData({ data: 1 });
```

</td>
</tr>
</table>

- Gdje god moguće, ako nema dodatnih pretvorbi, koristiti shorthand sintaksu za povratnu vrijednost funkcija za polja poput `map`, `reduce` i slično

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
const newItems = items.map((item) => {
  return {
    name: item.fullName,
    id: item.id
  };
});

const sum = items.reduce((acc, item) => {
  return acc + item.amount;
}, 0);
```

</td>
<td>

```typescript
const newItems = items.map((item) => ({
  name: item.fullName,
  id: item.id
}));

const sum = items.reduce((acc, item) => acc + item.amount, 0);
```

</td>
</tr>
</table>

- Koristiti `computed` umjesto funkcija koje vraćaju neku pretvorenu vrijednost, barem za **reaktivne** vrijednosti

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```html
<komponenta v-for="(item, i) in itemsFiltered()" :key="i" />
```

```typescript
const itemsFiltered = () => items.map((n) => n ** 2);
```

</td>
<td>

```html
<komponenta v-for="(item, i) in itemsFiltered" :key="i" />
```

```typescript
const itemsFiltered = computed(() => items.map((n) => n ** 2));
```

</td>
</tr>
</table>

- Uvijek koristiti `await`, rađe nego ulančavanje `then` callbacka prilikom asinkronih operacija

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
funkcija(1).then((res) => {
  console.log(res);
  const x = operacija(3);
});
```

</td>
<td>

```typescript
const res = await funkcija(1);
console.log(res);
const x = operacija(3);
```

</td>
</tr>
</table>

- Uvijek koristiti `T[]` kod definicije tipova polja umjesto `Array<T>`
- Redoslijed propova bi idealno trebao ići ovako:

1. "Statički" propovi
2. "Dinamički" propovi
3. Emitovi

```html
<komponenta
  prop-1="Prop"
  prop-2="5"
  :prop-3="4 + 5"
  :prop-4="x ** 2"
  @neki-emit="funkcija"
/>
```

- Redoslijed dijelova koda unutar `<script>` bi idealno trebao ići ovako:

1. Definicija `interface`-a za stanje i ostale dodatne modele po potrebi (kod Vue 2 ovo raditi van `defineComponent` funkcije)
2. Propovi
3. Emitovi
4. Composable funkcije
5. Konstantne varijable i `ref`-ovi
6. Stanje
7. Funkcije
8. `computed` vrijednosti
9. `watch`-evi
10. Lifecycle funkcije

```typescript
// 1
interface Item {
  description: string;
  amount: number;
}

interface State {
  name: string;
  id: number;
  items: Item[];
}

// 2
const props = defineProps({
  id: Number
});

// 3
const emit = defineEmits(["select-item", "do-something"]);

// 4
const router = useRouter();
const i18n = usei18n();

// 5
const headers = [{ text: "Text", value: "id" }];
const form = ref<IForm | null>(null);
const canvas = ref<HTMLCanvasElement | null>(null);

// 6
const state: State = reactive({
  name: "",
  id: 1,
  items: []
});

// 7
const doSomething = () => {
  emit("do-something", { x: 1 });
};

// 8
const itemsSum = computed(() =>
  items.reduce((acc, item) => acc + item.amount, 0)
);

// 9
watch(
  () => props.id,
  (val) => {
    console.log(val);
  }
);

// 10
onMounted(() => {
  console.log("Mounted!");
});
```

## Propovi

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

Ako nema potrebe za ostalim propertyima kod definicije propova, onda se može ostaviti samo tip:

```typescript
defineProps({
  name: String,
  id: Number
});
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

## Emitovi

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

## Reaktivno stanje

Kod definicije reaktivnog stanja koristi se ili `ref` ili `reactive`. Najbolje je uvijek koristiti `reactive` jer nije potrebno unwrappati referencu i kod velike količine podataka bolje je sve imati na jednom mjestu.

Svaki reaktivni objekt potrebno je tipizirati `interface`-om. **Ne** tipizirati stanje generičkim parametrom koji nudi `reactive` kako je [opisano u dokumentaciji](https://vuejs.org/guide/typescript/composition-api.html#typing-reactive).

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```typescript
interface State {
  a: number;
  b: string;
}

const state = reactive<State>({
  a: 1,
  b: "string"
});
```

</td>
<td>

```typescript
interface State {
  a: number;
  b: string;
}

const state: State = reactive({
  a: 1,
  b: "string"
});
```

</td>
</tr>
</table>

`ref` koristiti jedino kod povezivanja komponenti na sučelju sa `<script>`.

## Uvjetovani prikaz

Ako je potrebno uvjetovano prikazati više elemenata, wrappati ih unutar `<template>` komponente jer ona ne utječe na DOM.

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```html
<komponenta-a v-if="uvjet" /> <komponenta-b v-if="uvjet" />
```

</td>
<td>

```html
<template v-if="uvjet">
  <komponenta-a />
  <komponenta-b />
</template>
```

</td>
</tr>
</table>

## Petlje

Obavezno navesti `key` prop kod `v-for` petlje, po mogućnosti koristiti neki property od izvora podataka koji se iterira. Ako to nije mogućnost, onda koristiti drugi parametar prilikom iteracije tj. index.

```html
<komponenta v-for="item in items" :key="item.id" />
<komponenta v-for="(item, i) in items" :key="i" />
```

U slučaju da je potrebno uvjetovano prikazati petlju, wrappati je unutar `<template>` komponente.

```html
<template v-if="uvjet">
  <komponenta v-for="item in items" :key="item.id" />
</template>
```

## Slotovi

Slotove uvijek treba pisati skraćenim načinom pomoću `#`, npr. umjesto `v-slot:something` ide `#something`.

```html
<komponenta>
  <template #something="{ model }"> {{ model }} </template>
</komponenta>
```

Nikad ne izričito navoditi defaultni slot, već direktno stavljati isti sadržaj u tijelo elementa.

<table>
<tr align="center">
<td> 🟥 </td> <td> 🟩 </td>
</tr>
<tr>
<tr>
<td>

```html
<komponenta>
  <template #default> Tekst </template>
</komponenta>
```

</td>
<td>

```html
<komponenta> Tekst </komponenta>
```

</td>
</tr>
</table>

## Prijevodi

Za prijevode koristi se [i18n](https://kazupon.github.io/vue-i18n/) package.

`en.ts` se koristi za engleske i `hr.ts` za hrvatske prijevode, najbolje ih je organizirati po screenovima ili po nekim generalnim pojmovima.

## Validacija

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
          name="Language"
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

Svaki `vid` mora biti jedinstven, kao i `name` prop jer u protivnom dolazi do poremećaja u validaciji.

Iako je naveden `label` prop kod polja, mora biti naveden i u overridanom slotu za label ako je potrebno da polje bude `required`.

## Greške kod validacije

Najčešće greške kod validacije su `vid`-ovi koji nisu jedinstveni, tako da je potrebno osigurati da svaki `vid` polja za validaciju bude takav.

## Vuetify

- Gdje god moguće ako postoji gotova funkcionalnost na samoj komponenti, ne kombinirati druge komponente kako bi se postigla funkcionalnost, primjerice `v-data-table` i `v-pagination` jer `v-data-table` već ima funkcionalnost paginacije
- Overridanje Vuetify CSS klasa raditi uz pomoć `::v-deep` selectora
- Pravilno posložiti komponente i ugnijezditi ih na način kako je opisano u dokumentaciji osim u vanrednim situacijama, primjerice `v-card`:

```html
<v-card>
  <v-card-title> Title </v-card-title>
  <v-card-subtitle> Subtitle </v-card-subtitle>
  <v-card-text> Nešto </v-card-text>
  <v-card-actions>
    <v-btn> Klikni me </v-btn>
  </v-card-actions>
</v-card>
```

### Grid sistem

Gdje god moguće potrebno je koristiti `v-row` i `v-col` za organiziranje sučelja. Za svaki `v-col` navesti količinu stupaca preko `cols` propa, a po potrebi dalje za različite dimenzije.

Primjerice, pola za ekrane prosječne veličine (desktop itd.) i trećina za veće ekrane:

```html
<v-row>
  <v-col cols="12" md="6" xl="4"> Tekst </v-col>
</v-row>
```

### v-list

Pravilno posložiti popise kao što je opisano u dokumentaciji, a kod popisa gdje je potrebno imati odabranu stavku potrebno je pravilno handlati tu situaciju:

```html
<v-list>
  <v-list-item-group
    mandatory
    color="primary"
    v-model="state.selectedItem"
    @change="stationSelected"
  >
    <v-list-item v-for="(item, i) in items" :key="i" :value="item">
      <v-list-item-content>
        <v-list-item-title> {{ item.name }} </v-list-item-title>
      </v-list-item-content>
    </v-list-item>
  </v-list-item-group>
</v-list>
```

Koristiti ili `watch` funkciju ili `@change` listener za eventualne promjene nad listom, **nikad** ručno postavljati stavku jer `v-model` to već sam handla preko `value` propa navedenog na `<v-list-item>` komponenti

### v-data-table

S obzirom da se skoro uvijek koristi server-sided paginacija, organizirati komponentu na sljedeći način:

```html
<v-data-table
  item-key="id"
  :loading="state.loading"
  :headers="headers"
  :items="state.items"
  @update:options="getData"
  :options.sync="state.tableOptions"
  :footer-props="{ itemsPerPageOptions }"
  :server-items-length="state.totalItems"
/>
```

```typescript
interface State {
  items: Model[];
  tableOptions: IGridOptions;
  totalItems: number;
}

const state: State = reactive({
  items: [],
  tableOptions: {
    page: 1,
    itemsPerPage: 25,
    sortBy: [],
    sortDesc: [],
    groupBy: [],
    groupDesc: []
  },
  totalItems: 0
});

const headers = [
  {
    text: "Text",
    value: "id",
    sortable: true
  },
  {
    text: "",
    value: "actions",
    align: "right",
    width: "120",
    sortable: false
  }
];

const itemsPerPageOptions = [10, 25, 50];

const getData = async () => {
  state.loading = true;
  const { skip, take, sort, group } = transformVuetifyTableOptions(
    state.tableOptions
  );
  try {
    const { total, results } = await someService.getItems(
      skip,
      take,
      sort,
      group
    );
    state.totalItems = total;
    state.items = results;
  } finally {
    state.loading = false;
  }
};
```
