# In solution weaving


## Introduction

Sometimes it is necessary to have weaving code specific to a solution. In this case it is desirable to have that code exist in the same solution it is editing.

- _Note: Starting with Fody 4.0 the implicit lookup of weavers by naming conventions is no longer supported.
  Follow the instructions below on to migrate projects._


## Weaving inside a solution

Fody will [look for weavers by examining the `WeaverFiles` items](addin-discovery.md) of the project.


To enable in-solution weaving:

  1. Add a project named e.g. 'Weavers' to the solution.
  1. Add a class named `ModuleWeaver` to the project. See [ModuleWeaver](addin-development.md#weaver-project).
     _Note: The `ModuleWeaver` is the default name for the weaver class. It is also possible to use multiple weavers with different class names by specifying their class names in the `WeaverClassNames` attribute._
  1. Change the [solution build order](https://docs.microsoft.com/en-au/visualstudio/ide/how-to-create-and-remove-project-dependencies) so the 'Weavers' project is built before the projects consuming it. _Note: Do not add a project reference to the weaver_
  1. If a weaver class is explicitly specified in the `WeaverClassNames` attribute, Fody expects the configuration entry in the `FodyWeavers.xml` file to be named like the class. For example, if the class name is `NamedWeaver`:

```xml
<Weavers>
  <NamedWeaver />
</Weavers>
```

The [FodyAddinSamples](https://github.com/Fody/FodyAddinSamples/) repository provides examples of this.

 * Weavers project: https://github.com/Fody/FodyAddinSamples/tree/master/Weavers
 * The FodyWeavers.xml that includes those weavers: https://github.com/Fody/FodyAddinSamples/blob/master/Samples/FodyWeavers.xml
 * Test that verifies the weaving has occurred https://github.com/Fody/FodyAddinSamples/blob/master/Samples/InSolutionSample.cs

To **optionally** use a custom named weaver file for in solution weaving, add an entry to the project file:

```xml
<ItemGroup>
  <WeaverFiles
      Include="$(SolutionDir)Weavers\bin\$(Configuration)\Net46\Weavers.dll"
      WeaverClassNames="MyWeaver1;MyWeaver2" />
</ItemGroup>
```

The `WeaverClassNames` entry is optional. If not specified, Fody will look for a class named `ModuleWeaver`.
If the weaver assembly contains more than one weaver, specify the class names of all the weavers to use in the target project.