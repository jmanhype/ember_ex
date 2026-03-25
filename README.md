# EmberEx

Elixir port of the [Ember framework](https://github.com/pyember/ember) for building AI application pipelines with language models.

## Status

Experimental. The operator system, model providers, and JIT optimization layer compile and have tests, but there are no releases and no published hex package.

| Area | State |
|---|---|
| Operator system | Implemented: map, sequence, parallel, branch, ensemble, retry, LLM, verifier, selector/synthesis judge |
| Model providers | OpenAI provider implemented; other providers not yet |
| JIT optimization | 5 strategies: trace, structural, enhanced, LLM-specialized, stochastic |
| Specification/schema | Ecto-based input/output contracts |
| XCS execution engine | Graph-based with 4 schedulers (sequential, parallel, topological, wave) |
| Tests | 5 test files under `test/` |
| CI | No workflow files |
| Hex package | Not published |

## What it does

Provides composable operators for AI workflows:

1. Define operators (map, LLM call, branch, ensemble, etc.)
2. Compose them into sequences, parallel groups, or DAGs
3. Execute through the XCS engine with automatic scheduling
4. Optionally apply JIT optimization to operator chains

```elixir
# Create and compose operators
translate = EmberEx.Operators.LLMOperator.new("gpt-4o", "Translate to French: {input}", :text, :french)
uppercase = EmberEx.Operators.MapOperator.new(&String.upcase/1, :french, :result)
pipeline  = EmberEx.Operators.SequenceOperator.new([translate, uppercase])

result = EmberEx.Operators.Operator.call(pipeline, %{text: "Hello"})
```

### JIT optimization

```elixir
optimized = EmberEx.XCS.JIT.Core.jit(operator, mode: :llm)
```

Modes: `:trace`, `:structural`, `:enhanced`, `:llm`. The LLM mode separates deterministic pre/post-processing from stochastic LLM calls and caches the deterministic parts.

## Stack

| Dependency | Version |
|---|---|
| Elixir | ~> 1.14 |
| Ecto | ~> 3.10 |
| Finch | ~> 0.16 |
| HTTPoison | ~> 2.1 |
| instructor_ex | git (github) |
| Jason | ~> 1.4 |

## Repository layout

```
lib/ember_ex/
  operators/        13 operator modules
  models/           Provider abstraction, OpenAI provider, usage tracking
  specifications/   Ecto-based schema generator, specification pattern
  xcs/              Execution engine, graph, 4 schedulers
  xcs/jit/          JIT core, cache, profiler, 5 strategy modules
  examples/         7 example modules (assistant, RAG, ensemble, etc.)
  metrics/          Collector and storage
  context/          Execution context
scripts/            Benchmark and test scripts
test/               5 test modules
```

117 files total.

## Setup

```bash
git clone https://github.com/jmanhype/ember_ex.git
cd ember_ex
mix deps.get
mix test
```

Requires an `OPENAI_API_KEY` environment variable for any operator that calls a language model.

## Limitations

- Only the OpenAI provider is implemented. No Anthropic, Google, or local model support.
- The `instructor_ex` dependency is pinned to a git branch, not a hex release.
- No CI pipeline or automated test runs.
- No hex package published; install via git only.
- 5 test files cover operators and context; no tests for JIT, XCS engine, or model providers.
- No license file in the repository.

## License

Not specified.