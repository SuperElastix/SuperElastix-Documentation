.. _Development:

Development
===============

With SuperElastix we aim to capture a wide range of registration methods, accessible via a single high-level user interface.  At the core of our design is a single collection of components with heterogeneous levels of functionality and granularity. This means that a component can implement a single concept (metric, transform, etc.) to be reused in many methods, can implement multiple (tightly coupled) concepts in one, or even be a full registration algorithm. Breaking up algorithms in small components allows a user to mix-and-match component, whereas treating a larger part of algorithms as monolithic blocks lowers the barrier for integration of new methods and paradigms and reuse of existing codebases. 
By a high level user configuration, components are selected at run-time and are connected via a user-defined network layout. A generic handshake mechanism verifies whether connected components are compatible both mathematically as on a software level. The user is notified if specific combinations are incompatible or not supported (yet).


SuperElastixFilter input and output datatypes
---------------------------------------------

The SuperElastixFilter is designed to be part of an itk pipeline such that it can connect to other itk filters and supports the itk update mechanism. All input and output data required by the (configured) algorithm are exposed by the SuperElastixFilter. In this philosophy the SuperElastixFilter does/should not read or write data (images, meshes, etc) from disk directly. Therefore, in the commandline tool, which uses the SuperElastixFilter, readers and writers reside outside the SuperElastixFilter.
However, unlike common itk filters, the inputs and outputs of the SuperElastixFilter are typically unknown at compile time, because they depend on the Blueprint configuration describing the actual algorithm to execute. This complicates the setup of a pipeline, since up and downstream itk filters are typically templated over their datatypes.
To stay as close as possible to the itk philosophy, the SuperElastixFilter supports 2 modes of operation:

- *Application embedding*: at compile-time the inputs and outputs are known. That is, the application developer makes sure that any Blueprints to be used, will correspond to the (compile-time defined) number and types of inputs and outputs by known identifier names (defined by the Sink and Source Components). In this mode, the order in which the inputs and outputs are connected to other filters, and the Blueprint (Object) is set, is arbitrary. However, to connect the output of the SuperElastixFilter a templated version of GetOutput must be used: ImageFileReader<KnownImageType>::Pointer my_writer->SetInput(selxFilter->GetOutput<KnownImageType>(identifier));
- *Commandline tool*: at compile time the inputs and outputs are unknown. The class implementing the commandline interface is not aware of the datatypes used by all components. (In this way, adding custom components with new types does not affect the source code of the commandline interface). The commandline interface is invoked by pairs of filenames and identifier names. The identifiers refer to Sink or Source Components as defined via the Blueprint that, in turn, define the data types. In this mode, the commandline interface cannot instantiate the readers or writers because of their templated types. Instead, the SuperElastixFilter is requested to return appropriate readers and writers corresponding to the identifier names. SuperElastix will return respectively an AnyReader or AnyWriter, which are non-templated Base Classes that, if updated, call the appropriate reader of writer (by use of polymorphism). In this mode, it is required to set the Blueprint prior to request and connect readers or writers.

The following sequence diagrams show the order of function calls of each mode of operation.

.. image:: images/SeqDiagSelxFilterKnownTypes.png

.. image:: images/SeqDiagSelxFilterUnknownTypes.png

Note that these are simplified diagrams and may not reflect all details and naming as found in the source code.

SuperElastixFilter component database manipulation
--------------------------------------------------

We provide two library interfaces, each supporting a different use case:

- *"Precompiled" SuperElastix ITK filter*, designed to be used in external applications, such as the commandline interface or company applications.
 
- *"Templated" SuperElastix ITK filter*, offering the most flexibility, useful for external third-party components and extreme use cases.

In both cases SuperElastixFilter has an internal database of components that can be used to dynamically construct the registration algorithm of choice.
In the "Precompiled" library this database is populated with a predefined list of components (each with predefined template arguments, such as dimensionality and pixel type, etc). Predefinition of the components allows for hiding the implementation details of the components and speeds up the compilation process of the application (done via the Pimpl idiom). The "Precompiled" library is still and ITK filter and depends on the (templated) header files of the itk library.

In the "Templated" library the database of components can be populated by the user at compilation time by passing the component classes as template arguments. Applications using this library need access to all of SuperElastix internal source and header files at compilation time. This approach provides the flexibility to compile an instance of the SuperElastix ITK filter with, for instance, a sub- or superset of the default components, a set of components with exotic dimensionality or pixel types or even with third party components. Compiling the SuperElastix ITK filter with a small set of components is typically done in our Unit tests when testing a specific component or combination of components. Adding a third-party component to SuperElastix via template arguments does not require any modification of the source code files of the SuperElastixFilter. A third-party component can adhere to the existing already defined interfaces classes, but op top of that it can also define new interface classes.


User Component Creation
-----------------------

A SuperElastix Component consists of accepting and providing interfaces. To let the handshake mechanism handle a component correctly the component (class) must adhere to the following structure. The component class must derive from the :code:`SuperElastixComponent` class (solely). The :code:`SuperElastixComponent` is a templated class with signature :code:`< <Providing<I_A, I_B, ... >, Accepting<I_C, I_D, ... > > >`, with classes :code:`Providing` and :code:`Accepting` acting as placeholders to indicate the role of the interfaces :code:`I`.
By inheriting of the :code:`SuperElastixComponent` class the component developer needs to provide the implementation for a number of methods. These are:

- All methods that have been defined in the (abstract) interfaces classes that component developer selected. 

- A :code:`virtual void Set(I_x*)` for each interface class :code:`I_x` that has been selected as providing interface. (This example uses raw pointes, but in the reality we use code:`std::shared_ptr` for this).

- The :code:`virtual bool MeetsCriterion( const CriterionType & criterion )`, which returns true if and only if the component has an implementation for which the criterion (read from the Blueprint) holds.

.. ifconfig:: renderuml is 'False'

    .. image:: rendered/plantuml-d1f04ea4e651f964fcc5c4101b3d5b03121084b1.png

.. ifconfig:: renderuml is 'True'
    
    .. uml::
    
          @startuml
          
          'style options 
          skinparam monochrome true
          skinparam circledCharacterRadius 0
          skinparam circledCharacterFontSize 0
          skinparam classAttributeIconSize 0
          hide empty members
          
    	  class CustomComponent{
    	  type_A Method_A(args)
    	  type_B Method_B(args)
    	  void Set(I_C*)
    	  void Set(I_D*)
    	  bool MeetsCriterion()
    	  }
    	  
          class SuperElastixComponent< "<Providing<I_A, I_B, ... >, Accepting<I_C, I_D, ... > >" > {
    	  "HandShakeMethods"()
          }
    
          package Providing {
          class I_A << interface >> {
    	  type_A Method_A(args)
    	  }
          class I_B << interface >> {
    	  type_B Method_B(args)
          }
          }
    	  
          package Accepting {	  
          class "Acceptor<I_C>" << interface >> {
          void Set(I_C*)
          }
    	  
          class "Acceptor<I_D>" << interface >> {
          void Set(I_D*)
          }
    	  }
    	  
    	  class ComponentBase {
    	  bool MeetsCriterion()
    	  "HandShakeMethods"()
    	  }
    	  
          ComponentBase <|-- SuperElastixComponent
          I_A <|-- SuperElastixComponent
          I_B <|-- SuperElastixComponent
          "Acceptor<I_C>" <|-- SuperElastixComponent
          "Acceptor<I_D>" <|-- SuperElastixComponent
          
          SuperElastixComponent <|-- CustomComponent 
          @enduml
    
    	  