# Angular test patterns

## Standalone component

```ts
beforeEach(async () => {
  await TestBed.configureTestingModule({ imports: [UserCardComponent] }).compileComponents();
});

it('shows the user name', () => {
  const fixture = TestBed.createComponent(UserCardComponent);
  fixture.componentRef.setInput('user', { name: 'Dana' });
  fixture.detectChanges();
  expect(fixture.nativeElement.textContent).toContain('Dana');
});
```

## Service with HTTP

```ts
beforeEach(() => {
  TestBed.configureTestingModule({ providers: [provideHttpClient(), provideHttpClientTesting()] });
  http = TestBed.inject(HttpTestingController);
});
afterEach(() => http.verify());

it('loads users', () => {
  TestBed.inject(UserService).load().subscribe((users) => expect(users).toHaveLength(1));
  http.expectOne('/api/users').flush([{ id: 1 }]);
});
```

## Output event

```ts
const saved = vi.fn();
fixture.componentInstance.saved.subscribe(saved);
fixture.nativeElement.querySelector('button[type="submit"]').click();
expect(saved).toHaveBeenCalledWith({ name: 'Dana' });
```
