# Before and after

## Inputs, outputs, derived state

Before:

```ts
export class PriceTagComponent {
  @Input() amount = 0;
  @Input() currency = 'USD';
  @Output() selected = new EventEmitter<number>();
  get label() { return `${this.amount} ${this.currency}`; }
}
```

After:

```ts
export class PriceTagComponent {
  amount = input(0);
  currency = input('USD');
  selected = output<number>();
  label = computed(() => `${this.amount()} ${this.currency()}`);
}
```

## Service state

Before:

```ts
private readonly items$ = new BehaviorSubject<Item[]>([]);
readonly count$ = this.items$.pipe(map((i) => i.length));
add(item: Item) { this.items$.next([...this.items$.value, item]); }
```

After:

```ts
private readonly items = signal<Item[]>([]);
readonly count = computed(() => this.items().length);
add(item: Item) { this.items.update((i) => [...i, item]); }
```

## Template subscription

Before: `ngOnInit() { this.sub = this.api.user$.subscribe((u) => (this.user = u)); }`

After: `user = toSignal(this.api.user$);` and the template reads `user()`.
