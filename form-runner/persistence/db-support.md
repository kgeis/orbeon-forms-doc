# Database support

## Categories of databases

We have two categories of databases:

- __XML__: eXist
- __Relational__: Oracle, MySQL, SQL Server, PostgreSQL, DB2

Since Orbeon Forms 4.4, the implementation of relational support is common to all databases. There used to be separate implementation for each relational database.

For setup instructions, see [Using Form Runner with a relational database](relational-db.md).

## Feature matrix

Feature                                                                 |eXist        |Oracle       |MySQL        |SQL Server   |PostgreSQL   |DB2
------------------------------------------------------------------------|-------------|-------------|-------------|-------------|-------------|-------------
[Form controls and layouts including repeated grids and sections][blog1]|Y            |Y            |Y            |Y<sup>4</sup>|Y<sup>6</sup>|Y<sup>1</sup>
[Versioning][blog2]                                                     |N            |Y<sup>3</sup>|Y<sup>3</sup>|Y<sup>4</sup>|Y<sup>6</sup>|Y<sup>3</sup>
[Owner/group-based permissions](../access-control/owner-group.md)       |Y<sup>6</sup>|Y<sup>2</sup>|Y<sup>1</sup>|Y<sup>4</sup>|Y<sup>6</sup>|Y<sup>1</sup>
[Autosave](autosave.md)                                                 |N            |Y<sup>2</sup>|Y<sup>1</sup>|Y<sup>4</sup>|Y<sup>6</sup>|Y<sup>1</sup>
[Flat view](flat-view.md)                                               |N/A          |Y            |N            |N            |Y<sup>6</sup>|Y<sup>5</sup>
Orbeon Forms PE support                                                 |Y            |Y            |Y            |Y            |Y            |Y
Orbeon Forms CE support                                                 |Y            |N            |Y            |N            |Y            |N

1. Since Orbeon Forms 4.3.
1. Since Orbeon Forms 4.4.
1. Since Orbeon Forms 4.5.
1. Since Orbeon Forms 4.6.
1. Since Orbeon Forms 4.7.
1. Since Orbeon Forms 4.8.

## Third-party implementations

A third-party [MarkLogic persistence layer for Orbeon Form Runner](https://gitlab.dyomedea.com/marklogic/orbeon-form-runner-persistence-layer/tree/master) is available. As of 2015-03-02, this does not support versioning and owner/group-based permissions. Also please note that this is currently not officially supported by Orbeon.

[blog1]: http://blog.orbeon.com/2014/01/repeated-sections.html
[blog2]: http://blog.orbeon.com/2014/02/form-versioning.html

## See also 

- [Using Form Runner with a relational database](relational-db.md)

