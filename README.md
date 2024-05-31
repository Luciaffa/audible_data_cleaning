# Introduction
In this project, I explore techniques for cleaning and transforming data using SQL. Data cleaning is a critical step in the data preparation process, ensuring that the data we work with is accurate, consistent, and ready for analysis.

# Background
The data source for this project include an Audible dataset obtained from Kaggle. This dataset contains information about audiobooks available on Audible.in, providing details such as book titles, authors, genres, ratings, and more. By applying data cleaning techniques, I aim to transform raw, unstructured data into a clean and usable format, ready for further analysis or integration into other systems.

**Dataset sample**

[![Screenshot-2024-05-31-174640.png](https://i.postimg.cc/FH5zfp9L/Screenshot-2024-05-31-174640.png)](https://postimg.cc/6yctgd4W)

| Name                                      | Author                                       | Narrator                                 | Time               | Release Date | Language | Stars                      | Price |
|-------------------------------------------|----------------------------------------------|------------------------------------------|--------------------|--------------|----------|----------------------------|-------|
| The Dragonet Prophecy                     | Writtenby:TuiT.Sutherland                    | Narratedby:ShannonMcManus                | 8 hrs and 32 mins  | 01-07-12     | English  | 5 out of 5 stars11 ratings | 820.00|
| Dangerous Gift                            | Writtenby:TuiT.Sutherland                    | Narratedby:ShannonMcManus                | 7 hrs and 51 mins  | 02-03-21     | English  | 5 out of 5 stars9 ratings  | 445.00|
| "The 39 Clues, Book 6"                    | Writtenby:JudeWatson                         | Narratedby:DavidPittu                    | 5 hrs and 10 mins  | 06-11-09     | English  | 5 out of 5 stars4 ratings  | 468.00|
| Never Say Genius                          | Writtenby:DanGutman                          | Narratedby:MichaelGoldstrom              | 5 hrs and 27 mins  | 23-08-13     | English  | 5 out of 5 stars8 ratings  | 585.00|
| The Madman of Black Bear Mountain         | Writtenby:FranklinW.Dixon                    | Narratedby:TimGregory                    | 2 hrs and 54 mins  | 07-06-16     | English  | 5 out of 5 stars3 ratings  | 305.00|
| Unicorn Academy: Zara and Moonbeam        | Writtenby:JulieSykes                         | Narratedby:KristinAtherton               | 1 hr and 35 mins   | 10-03-22     | English  | Not rated yet              | 266.00|
| El fantasma de la bodega                  | Writtenby:FranciscoDíazValladares            | Narratedby:NuriaSamsó                    | 2 hrs and 23 mins  | 04-04-22     | spanish  | Not rated yet              | 192.00|
| El paseo encantado                        | Writtenby:Mattel                             | Narratedby:VanessaPérezJurado            | 33 mins            | 01-04-22     | spanish  | Not rated yet              | 76.00 |
| Wigetta                                   | Writtenby:Vegetta777,Willyrex                | Narratedby:ÁlexdePorrata                 | 3 hrs and 37 mins  | 05-04-22     | spanish  | Not rated yet              | 192.00|
| Aru Shah and the Nectar of Immortality    | Writtenby:RoshaniChokshi                     | Narratedby:SoneelaNankani                | 11 hrs and 19 mins | 05-04-22     | English  | Not rated yet              | 821.00|
| Barnabas der Wolkenschaufler              | Writtenby:SophieSchoenwald,NadineReitz-Illustrator | Narratedby:BerndReheuser           | 2 hrs and 56 mins  | 04-04-22     | german   | Not rated yet              | 200.00|
| The Boy in the Post                       | Writtenby:HollyRivers                        | Narratedby:SusieTrayling                 | 6 hrs and 58 mins  | 01-04-22     | English  | Not rated yet              | 706.00|
| Thornwood                                 | Writtenby:LeahCypess                         | Narratedby:JessicaAlmasy                 | 5 hrs and 41 mins  | 05-04-22     | English  | Not rated yet              | 904.00|
| 7 histoires d'aventures !                 | Writtenby:BertrandFichou,NoraThullin | Narratedby:GuyChappelier,FrédéricSanchez,MarieZidi | 43 mins | 11-02-22 | french   | Not rated yet              | 679.00|
| Le Loup qui devenait chef de la forêt     | Writtenby:OrianneLallemand                   | Narratedby:WillProduction                | 9 mins             | 01-04-22     | french   | Not rated yet              | 112.00|
| Curse of the Robo-Dragon                  | Writtenby:AdamBlade                          | Narratedby:TheoSolomon                   | 2 hrs and 6 mins   | 01-04-22     | English  | Not rated yet              | 531.00|
| Call of the Crow                          | Writtenby:JessicaKhoury                      | Narratedby:MichaelGallagher,EmilyEiden   | 9 hrs and 30 mins  | 05-04-22     | English  | Not rated yet              | 586.00|
| Glass Slippers                            | Writtenby:LeahCypess                         | Narratedby:KeylorLeigh                   | 5 hrs and 38 mins  | 05-04-22     | English  | Not rated yet              | 904.00|

<!--End of table-->

# What are the data quality improvements needed?
