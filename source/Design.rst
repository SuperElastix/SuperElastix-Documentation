.. _Design:

Design
======

With SuperElastix we aim to capture a wide range of registration methods, accessible via a single high-level user interface.  At the core of our design is a single collection of components with heterogeneous levels of functionality and granularity. This means that a component can implement a single concept (metric, transform, etc.) to be reused in many methods, can implement multiple (tightly coupled) concepts in one, or even be a full registration algorithm. Breaking up algorithms in small components allows a user to mix-and-match component, whereas treating a larger part of algorithms as monolithic blocks lowers the barrier for integration of new methods and paradigms and reuse of existing codebases. By a high level user configuration, components are selected at run-time and are connected via a user-defined network layout. A generic handshake mechanism verifies whether connected components are compatible both mathematically as on a software level. The user is notified if specific combinations are incompatible or not supported (yet).

Network of components
---------------------

A classical approach for a registration toolbox is to use an object-oriented design that decomposes the registration problem into
classes like metrics, transforms, optimizers, etc. Extended types of behavior (mutual information, affine transform, etc) can be implemented via subtyping. Such a decomposition, however, might not be adequate to handle all of the diversity in existing registration paradigms. And, even within the same paradigm existing toolboxes have slighly different decomposions. Unification of registration methods by refactoring existing toolboxes into one object-oriented design is a tremendous amount of work, if possible at all. Additionally, an object-oriented design typically poses a rather rigid layout how e.g. an optimizer interacts with a metric and a tranforms. Within the large variety of paradigms, there seems to be little consensus to a standard organization. The design of SuperElastix, therefore, takes a different approach. A registration algorithm is considered to be a network of functional components. The network layout (i.e. amount of nodes and connections) is user configurable and the components (i.e. the nodes) are treated uniformely. In our framework each component formalizes its interfaces that determines whether components can connect. Components can have diverse functionalities such of mutual information, affine transform, or a full registration pipeline, but all have a generic mechanism to perform a handshake. 

.. figure:: rendered/plantuml-f486c5a4288c3031a13f4bb7e22e0ebf57b0aee3.png

    A user configurable network consisting of many connected Components.
    
.. ifconfig:: renderuml is 'True'
    
    .. uml::
        
       
        @startuml
          
          'style options 
          skinparam monochrome true
          skinparam circledCharacterRadius 0
          skinparam circledCharacterFontSize 0
          skinparam classAttributeIconSize 0
          hide empty members

          rectangle NetworkBuilder {
            component "Source 1" as Source1{
            }
            component "Source 2" as Source2{
            }
            component "Component A" as CompA {
            }
            component "Component B" as CompB {
            }
            component "Component C" as CompC {
            }
            component Sink {
            }

            Source1 -0)- CompA
            Source2 -0)- CompA
            CompB -right0)- CompA
            CompA -0)- CompC
            Source1 -0)- CompC
            CompC -0)- Sink
          }
          "Input 1" ..> Source1
          "Input 2" ..> Source2
          Sink ..> Output
        @enduml
        
.. figure:: rendered/plantuml-40e7ef9675820ed0b2587bdb9b3f3b8037704a43.png

    Components can fulfill many tasks upto a full registration pipeline.
        
.. ifconfig:: renderuml is 'True'
    
    .. uml::
    
        @startuml
          
          'style options 
          skinparam monochrome true
          skinparam circledCharacterRadius 0
          skinparam circledCharacterFontSize 0
          skinparam classAttributeIconSize 0
          hide empty members


          rectangle NetworkBuilder {
            component "Source 1" as Source1{
            }
            component "Source 2" as Source2{
            }
            component "Full Registration Component" as CompA {
            }
            component Sink {
            }

            Source1 -0)- CompA
            Source2 -0)- CompA
            CompA -0)- Sink
          }
          "Input 1" ..> Source1
          "Input 2" ..> Source2
          Sink ..> Output
        @enduml

Generic handshake mechanism
---------------------------
        
.. figure:: rendered/plantuml-15f09364c0dd497ffc5eb416a8c0f1e50f3036b7.png

    The handshake mechanism validates whether components can be connected or not.

.. ifconfig:: renderuml is 'True'
    
    .. uml::
    
        @startuml
          
          'style options 
          skinparam monochrome true
          skinparam circledCharacterRadius 0
          skinparam circledCharacterFontSize 0
          skinparam classAttributeIconSize 0
          hide empty members

		  rectangle NetworkBuilder {
          component "Component A" as CompA {
          }
        
          component "Component B" as CompB{
          }

            () " " as I_Before1
            () " " as I_Before2
		  
          cloud "Hand shake mechanism" {
            () "<<interface>> I_A" as I_A
            () "<<interface>> I_B" as I_B
            () "<<interface>> I_C" as I_C
			() "<<interface>> I_D" as I_D
          }

            () " " as I_After1
            () " " as I_After2

          
          I_Before1 )-down- CompA
          I_Before2 )-down- CompA
          CompA -down-() I_B : "providing I_B"
          CompA -down-() I_C : "providing I_C"
          CompA -down-() I_D : "providing I_D"
          I_A )-down- CompB : "Acceptor<I_A>"
          I_B )-down- CompB : "Acceptor<I_B>"
          I_C )-down- CompB : "Acceptor<I_C>"
          CompB -down-() I_After1
          CompB -down-() I_After2
		  
          }
        @enduml
        
Prior to running the registration algorithm contructed from a user-defined network, SuperElastix 
validates whether components can be connected or not. This hand mechanism
performs the necessary checks on what a component can do (as defined by its interfaces), which is
required to establish a connection. The advantage of explicitly
handling this generically and on a higher level, is that components
themselves do not need to perform these checks on neighboring
components, which would require a component to embed specific
knowledge about other components.

To manage all possible types of collaboration, SuperElastix maintains
an extensible collection of component interfaces. Any component in the toolbox must be defined
in terms of one or more interfaces, which are either
accepting or providing. After succesfull handshakes all components check
if sufficient accepting interfaces have been connected.
The underlying implementation details can be found in the development section.
