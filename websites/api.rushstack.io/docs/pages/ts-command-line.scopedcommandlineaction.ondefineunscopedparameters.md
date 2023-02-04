---
hide_title: true
custom_edit_url: null
pagination_prev: null
pagination_next: null
---
<!-- Do not edit this file. It is automatically generated by API Documenter. -->

[Home](./index.md) &gt; [@rushstack/ts-command-line](./ts-command-line.md) &gt; [ScopedCommandLineAction](./ts-command-line.scopedcommandlineaction.md) &gt; [onDefineUnscopedParameters](./ts-command-line.scopedcommandlineaction.ondefineunscopedparameters.md)

## ScopedCommandLineAction.onDefineUnscopedParameters() method

The child class should implement this hook to define its unscoped command-line parameters, e.g. by calling defineFlagParameter(). At least one scoping parameter must be defined. Scoping parameters are defined by setting the parameterGroupName to ScopedCommandLineAction.ScopingParameterGroupName.

**Signature:**

```typescript
protected onDefineUnscopedParameters?(): void;
```
**Returns:**

void
