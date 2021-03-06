---
title: Proprietà Derivate
type: guida
order: 5
---

Le proprietà in-template sono molto comode per alcuni piccoli task dove le espressioni non sono complesse. Inserire troppa logica, calcolata al momento, in un template strutturato e complesso può diventare difficile da mantenere. Ecco perché Vue.js limita la logica di una proprietà in-template ad una sola espressione, quasi primitiva. Per qualsiasi altro scopo, più complesso, esistono le **Proprietà Derivate**.

### Esempio Iniziale

``` html
<div id="example">
  a={{ a }}, b={{ b }}
</div>
```

``` js
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    // Un getter derivato
    b: function () {
      // `this` punta alla istanza vm
      return this.a + 1
    }
  }
})
```

Risultato:

{% raw %}
<div id="example" class="demo">
  a={{ a }}, b={{ b }}
</div>
<script>
var vm = new Vue({
  el: '#example',
  data: {
    a: 1
  },
  computed: {
    b: function () {
      return this.a + 1
    }
  }
})
</script>
{% endraw %}

Abbiamo dichiarato una proprietà derivata `b`. La funziona che implementa questa properità derivata è un Getter per la proprietà `vm.b`:

``` js
console.log(vm.b) // -> 2
vm.a = 2
console.log(vm.b) // -> 3
```

Potete aprire la console del browser e provare a cambiare il valore di `a` e vedere come si riflette su `b` dato che quest'ultimo deriva da `a`.

Le properità derivate possono essere vincolate all'interno del template come una qualsiasi properità normale. Vue.js sa che `vm.b` deriva da `vm.a`, perciò aggiornerà `vm.b` ogni volta che `vm.a` acquisisce un nuovo valore. La parte più interessante è che abbiamo creato questa dipendenza in modo dichiarativo: La funzione che implementa la properità derivata è primitiva, facile da testare e non ha effetti collaterali su altre proprietà.

### Proprietà Derivate vs. $watch

Vue.js fornisce un metodo chiamato `$watch`, quest'ultimo permette di osservare il cambiamento di alcuni dati dell'istanza Vue. Quando si hanno delle proprietà che derivano da altre proprietà, come nel nostro esempio precedente, c'è la tentazione di sfruttare `$watch` - specialmente se si arriva da un ambiente come AngularJS. Però è quasi sempre consigliato usare una properietà derivata rispetto ad un `$watch`. Considerate questo esempio:

``` html
<div id="demo">{{fullName}}</div>
```

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar',
    fullName: 'Foo Bar'
  }
})

vm.$watch('firstName', function (val) {
  this.fullName = val + ' ' + this.lastName
})

vm.$watch('lastName', function (val) {
  this.fullName = this.firstName + ' ' + val
})
```

Il codice sovrastante è riperitivo ed imperativo. Confrontatelo con una properietà derivata:

``` js
var vm = new Vue({
  data: {
    firstName: 'Foo',
    lastName: 'Bar'
  },
  computed: {
    fullName: function () {
      return this.firstName + ' ' + this.lastName
    }
  }
})
```

Molto meglio, non trovate?

### Setter Derivati

Le properietà derivate tendenzialmente vengono usate solo come getter, ma puoi comunuqe specificare un setter nel caso vi serva:

``` js
// ...
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
// ...
```

In questo caso, quando chiamerete `vm.fullName = 'John Doe'`, il setter verrà invocato e `vm.firstName`, `vm.lastName` verranno aggiornati automaticamente.

I dettagli tecnici di come funzionano le properità derivate vengono [discussi in un altro capitolo](reactivity.html#Inside-Computed-Properties) nel quale si parla del sistema Reattivo.
