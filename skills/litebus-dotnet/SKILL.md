---
name: litebus-dotnet
description: LiteBus mediator guidance for .NET applications using CQS, DDD, commands, queries, events, pipeline handlers, open generic handlers, and durable command inbox. Use when implementing, configuring, migrating to, testing, or troubleshooting LiteBus, LiteBus.Commands, LiteBus.Queries, LiteBus.Events, LiteBus.Messaging, Microsoft DI integration, Autofac integration, or MediatR-to-LiteBus changes.
---

# LiteBus .NET

Use this skill for LiteBus, the in-process .NET mediator for Command Query Separation (CQS), DDD-style application layers, and semantic command/query/event pipelines.

## Official Sources

- Repository: https://github.com/litenova/LiteBus
- Wiki: https://github.com/litenova/LiteBus/wiki
- Cheat sheet: https://github.com/litenova/LiteBus/wiki/LiteBus-Cheat-Sheet
- Getting started: https://github.com/litenova/LiteBus/wiki/Getting-Started
- Best practices: https://github.com/litenova/LiteBus/wiki/Best-Practices
- Testing: https://github.com/litenova/LiteBus/wiki/Testing-LiteBus

Check the installed LiteBus package version before applying migration advice.

## Quick Start

For Microsoft DI, install only the modules the app uses:

```powershell
dotnet add package LiteBus.Commands.Extensions.Microsoft.DependencyInjection
dotnet add package LiteBus.Queries.Extensions.Microsoft.DependencyInjection
dotnet add package LiteBus.Events.Extensions.Microsoft.DependencyInjection
```

Use Autofac extension packages only when the app uses Autofac. Keep domain/application projects on `.Abstractions` packages when they only need message and handler interfaces.

## Registration

```csharp
builder.Services.AddLiteBus(liteBus =>
{
    var appAssembly = typeof(Program).Assembly;
    liteBus.AddCommandModule(module => module.RegisterFromAssembly(appAssembly));
    liteBus.AddQueryModule(module => module.RegisterFromAssembly(appAssembly));
    liteBus.AddEventModule(module => module.RegisterFromAssembly(appAssembly));
});
```

Register handlers at startup. `RegisterFromAssembly` discovers concrete and open generic handlers in the scanned assembly.

## Message Example

```csharp
public sealed record CreateProductCommand(string Name, decimal Price) : ICommand<Guid>;

public sealed class CreateProductCommandHandler : ICommandHandler<CreateProductCommand, Guid>
{
    public Task<Guid> HandleAsync(CreateProductCommand command, CancellationToken cancellationToken)
        => Task.FromResult(Guid.NewGuid());
}

public sealed record GetProductByIdQuery(Guid Id) : IQuery<ProductDto>;
public sealed record ProductCreatedEvent(Guid ProductId, string Name);
```

- Commands change state and are handled by exactly one handler.
- Queries read data and are handled by exactly one handler.
- Stream queries use `IStreamQuery<TResult>` and return `IAsyncEnumerable<TResult>`.
- Events notify that something happened and can have zero, one, or many handlers.
- Events can be POCO records; they do not always need to implement `IEvent`.

## Workflow

1. Inject `ICommandMediator`, `IQueryMediator`, or `IEventPublisher` rather than a generic mediator abstraction.
2. Use validators or pre-handlers for validation, authorization, and guard clauses.
3. Use post-handlers for side effects after successful handling.
4. Use error handlers for centralized exception logging, metrics, or translation.
5. Use open generic handlers for universal concerns like logging and validation.
6. Use `[HandlerPriority]`, `[HandlerTag]`, mediation settings, and `AmbientExecutionContext.Current` only when the pipeline needs those features.

## Design Rules

- Make messages immutable with records or init-only properties.
- Name commands with imperative intent, queries by requested data, and events in past tense.
- Return DTOs or view models from queries, not domain entities.
- Keep handlers stateless and inject dependencies through constructors.
- Avoid mediator calls from inside handlers; use explicit orchestration, post-handlers, sagas, or process managers for workflows.
- Design durable inbox commands to be idempotent because processing is at least once.

## Testing

- Unit test handlers directly without LiteBus infrastructure.
- Integration test important pipelines through a real service provider.
- Mock semantic mediator interfaces only when testing callers such as controllers.
- Keep integration tests isolated with a fresh service provider or cleared shared registries.

## MediatR Mapping

- State-changing `IRequest<TResult>` -> `ICommand<TResult>`.
- Read-only `IRequest<TResult>` -> `IQuery<TResult>`.
- `INotification` -> LiteBus events; POCO events are supported.
- `IStreamRequest<TResult>` -> `IStreamQuery<TResult>`.
- `IPipelineBehavior<,>` -> pre-handlers, post-handlers, error handlers, validators, or open generic handlers.
