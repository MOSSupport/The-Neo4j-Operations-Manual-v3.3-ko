# Chapter 1. Introduction

> This chapter introduces Neo4j.

  
Neo4j is the world’s leading graph database. It is built_from the ground up_to be a graph database, meaning that its architecture is designed for optimizing fast management, storage, and the traversal of nodes and relationships. Therefore, relationships are described as_first class citizens_in Neo4j.

In the world of relational databases the performance of a_join_operation will degrade exponentially with the number of relationships. However, in Neo4j the corresponding action is performed as navigation from one node to another; an operation whose performance is linear.

This different approach to storing and querying connections between entities provides traversal performance of up to four million hops per second and core. Since most graph searches are local to the larger neighborhood of a node, the total amount of data stored in a database will not affect operations runtime. Dedicated memory management, and highly scalable and memory efficient operations, contribute to the benefits.

The property graph approach is also_whiteboard friendly_. By this we mean that the schema-optional model of Neo4j provides for a consistent use of the same model throughout conception, design, implementation, storage, and visualization. A major benefit of this is that it allows all business stakeholders to participate throughout the development cycle. Additionally, the domain model can be evolved continuously as requirements change, without the penalty of expensive schema changes and migrations.

Cypher, the declarative graph query language, is designed to visually represent graph patterns of nodes and relationships. This highly capable, yet easily readable, query language is centered around the patterns that express concepts and questions from a specific domain. Cypher can also be extended for narrow optimizations for specific use cases.

Neo4j can store trillions of entities for the largest datasets imaginable while being sensitive to compact storage. For production environments it can be deployed as a scalable, fault-tolerant cluster of machines. Due to its high scalability, Neo4j clusters require only tens of machines, not hundreds or thousands, saving on cost and operational complexity. Other features for production applications include hot backups and extensive monitoring.

## 

  


