# Application mapping recipes

All helpers use version `qentra-sha256-v1`, expanding recorded output with domain-separated SHA-256 and rejection sampling over 32-bit draws. Expansion does not add entropy. Store the mapping configuration with the output so another reader can reproduce the outcome.

## Select tournament winners

```ts
const winners = await client.applyMapping(id, {
  kind: 'pickUnique', items: ['Amber', 'Birch', 'Cedar', 'Dune'], count: 2,
});
```

Labels must be distinct for selection without replacement. Count is at least one and no greater than the pool length. Returned labels cannot repeat.

## Shuffle allocation applicants

```ts
const allocation = await client.applyMapping(id, {
  kind: 'shuffle', items: ['Applicant A', 'Applicant B', 'Applicant C'],
});
```

The Fisher-Yates shuffle returns a permutation without mutating the caller's array. It accepts 1 to 1000 nonempty string labels, each at most 200 characters.

## Sample agent candidates

```ts
const candidate = await client.applyMapping(id, {
  kind: 'weightedPick', items: ['Candidate A', 'Candidate B'], weights: [3, 1],
});
```

Use nonnegative integer weights with a positive total at most 2^32. A zero-weight entry cannot be selected. Each label needs exactly one weight.

## Roll an inclusive integer

```ts
const roll = await client.applyMapping(id, { kind: 'uniformInt', min: 1, max: 6 });
```

Both limits are signed 32-bit integers; maximum is at least minimum. Full signed-range mapping is supported. Never replace rejection sampling with a direct remainder of a sample for non-divisor ranges.
