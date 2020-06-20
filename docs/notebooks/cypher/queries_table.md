# Summary Table

|Created|Title|Description|Author(s)|Query|
| :---| :---| :---| :---|:---|
|2020/06/20 |Servers a user can RDP into |Find servers a user can RDP into. |Ryan Hausknecht (@haus3c) |```MATCH p=(g:Group)-[:CanRDP]->(c:Computer) WHERE  g.objectid ENDS WITH '-513' AND c.operatingsystem CONTAINS 'Server' RETURN p   ```|
|2020/06/20 |View all GPOs that contain a keyword |View all GPOs that contain a keyword |Ryan Hausknecht (@haus3c) |```MATCH (n:GPO) WHERE n.name CONTAINS "DOMAIN" RETURN n```|
|2020/06/20 |Domain Users Groups with Interesting ACEs |Find interesting privileges/ACEs that have been configured to DOMAIN USERS group |Ryan Hausknecht (@haus3c), Roberto Rodriguez (@Cyb3rWard0g) |```MATCH (m:Group) WHERE m.name =~ 'DOMAIN USERS@CONTOSO.LOCAL' MATCH p=(m)-[r:Owns|WriteDacl|GenericAll|WriteOwner|ExecuteDCOM|GenericWrite|AllowedToDelegate|ForceChangePassword]->(n:Computer) RETURN p```|
|2020/06/20 |Top 10 Computers with Most Admins |List of top 10 computers with most admins |Walter.Legowski (@SadProcessor) |```MATCH (n:User),(m:Computer),(n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH m,count(r) as rel_count  ORDER BY rel_count desc  LIMIT 10  MATCH (m)<-[r:AdminTo]-(n)  RETURN n,r,m```|
|2020/06/20 |Map Domain Trusts |Map domain trusts |Walter.Legowski (@SadProcessor) |```MATCH (n:Domain) MATCH p=(n)-[r]-() RETURN p```|
|2020/06/20 |High Value Target Group |Show all high value target group |Ryan Hausknecht (@haus3c) |```MATCH p=(n:User)-[r:MemberOf*1..]->(m:Group {highvalue:true}) RETURN p```|
|2020/06/20 |Shortest Path to DA Groups from Domain Users Groups |Shortest paths to Domain Admins group from the Domain Users group |Ryan Hausknecht (@haus3c) |```MATCH (g:Group) WHERE g.name =~ 'DOMAIN USERS@.*' MATCH (g1:Group) WHERE g1.name =~ 'DOMAIN ADMINS@.*' OPTIONAL MATCH p=shortestPath((g)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(g1)) RETURN p```|
|2020/06/20 |ASP-REQ Roastable Users |Find user that doesn’t require kerberos pre-authentication (aka AS-REP Roasting) |Ryan Hausknecht (@haus3c) |```MATCH (u:User {dontreqpreauth: true}) RETURN u```|
|2020/06/20 |Unprivileged Users with Rights to Add Members to Groups |Find if unprivileged users have rights to add members into groups |Ryan Hausknecht (@haus3c) |```MATCH (n:User {admincount:False}) MATCH p=allShortestPaths((n)-[r:AddMember*1..]->(m:Group)) RETURN p```|
|2020/06/20 |Users that Logged in ithin Threshold |Find users that logged in within the last 90 days. Change 90 to whatever threshold you want. |Ryan Hausknecht (@haus3c) |```MATCH (u:User) WHERE u.lastlogon < (datetime().epochseconds - (90 * 86400)) and NOT u.lastlogon IN [-1.0, 0.0] RETURN u.name```|
|2020/06/20 |Shortest Path to DA Groups from Non-Privileged Domain Users |Shortest paths to Domain Admins group from non privileged users (AdminCount=false) |Ryan Hausknecht (@haus3c) |```MATCH (n:User {admincount:false}),(m:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p```|
|2020/06/20 |Unsupported OSs |Find unsupported OSs |Ryan Hausknecht (@haus3c) |```MATCH (H:Computer) WHERE H.operatingsystem =~ '.*(2000|2003|2008|xp|vista|7|me)*.' RETURN H.name```|
|2020/06/20 |Top 10 Users with Most Sessions |List Top 10 Users with Most Sessions |Walter.Legowski (@SadProcessor) |```MATCH (n:User),(m:Computer),(n)<-[r:HasSession]-(m)  WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON'  AND NOT n.name='' WITH n,  count(r) as rel_count  order by rel_count desc  LIMIT 10  MATCH (m)-[r:HasSession]->(n)  RETURN n,r,m```|
|2020/06/20 |Users with Passwords Last Set withing Threshold |Find users with passwords last set thin the last 90 days. Change 90 to whatever threshold you want. |Ryan Hausknecht (@haus3c) |```MATCH (u:User) WHERE u.lastlogon < (datetime().epochseconds - (90 * 86400)) and NOT u.lastlogon IN [-1.0, 0.0] RETURN u.name```|
|2020/06/20 |Shortest Path to DA Groups from Computers |Shortest paths to Domain Admins group from computers |Ryan Hausknecht (@haus3c), Roberto Rodriguez (@Cyb3rWard0g) |```MATCH (n:Computer),(m:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p```|
|2020/06/20 |All Domain Users CanRDP Edges Against all Computers |Find only the CanRDP privileges (edges) of the domain users against the domain computers |Ryan Hausknecht (@haus3c) |```MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(u1)-[:CanRDP*1..]->(c:Computer) RETURN p2```|
|2020/06/20 |All Domain Users AdminTo Edges Against all Computers |Find only the AdminTo privileges (edges) of the domain users against the domain computers |Ryan Hausknecht (@haus3c) |```MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(u1)-[:AdminTo*1..]->(c:Computer) RETURN p2```|
|2020/06/20 |Active Users Sessions in all Domain Computers |Find the active user sessions on all domain computers |Ryan Hausknecht (@haus3c) |```MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(c:Computer)-[*1]->(u1) RETURN p2```|
|2020/06/20 |Kerberoastable Users with a path to DA |Find Kerberoastable Users with a path to DA |Ryan Hausknecht (@haus3c) |```MATCH (u:User {hasspn:true}) MATCH (g:Group) WHERE g.name CONTAINS 'DOMAIN ADMINS' MATCH p = shortestPath( (u)-[*1..]->(g) ) RETURN p```|
|2020/06/20 |Kerberoastable Users |Find All Users with an SPN/Find all Kerberoastable Users |Ryan Hausknecht (@haus3c) |```MATCH (n:User)WHERE n.hasspn=true RETURN n.name```|
|2020/06/20 |Specific Users Edges to All Nodes |Find all Edges that a specific user has against all the nodes (HasSession is not calculated, as it is an edge that comes from computer to user, not from user to computer) |Ryan Hausknecht (@haus3c) |```MATCH (n:User) WHERE n.name =~ 'JEFFMCJUNKIN@CONTOSO.LOCAL' MATCH (m) WHERE NOT m.name = n.name MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(m)) RETURN p LIMIT 10```|
|2020/06/20 |Shortest Path to DA Groups from Domain Groups |Shortest paths to Domain Admins group from all domain groups |Ryan Hausknecht (@haus3c) |```MATCH (n:Group) WHERE NOT n.name = 'DOMAIN ADMINS@CONTOSO.LOCAL' MATCH (m:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p```|
|2020/06/20 |Shortest Path to DA Groups from Non-Privileged Domain Groups |Shortest paths to Domain Admins group from non-privileged groups (AdminCount=false) |Ryan Hausknecht (@haus3c) |```MATCH (n:Group {admincount:false}),(m:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p```|
|2020/06/20 |Shortest Path to DA Groups from Computers Excluding DCs |Shortest paths to Domain Admins group from computers excluding potential DCs (based on ldap/ and GC/ spns) |Ryan Hausknecht (@haus3c) |```WITH '(?i)ldap/.*' as regex_one WITH '(?i)gc/.*' as regex_two MATCH (n:Computer) WHERE NOT ANY(item IN n.serviceprincipalnames WHERE item =~ regex_two OR item =~ regex_two ) MATCH(m:Group {name:"DOMAIN ADMINS@CONTOSO.LOCAL"}),p=shortestPath((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct*1..]->(m)) RETURN p```|
|2020/06/20 |Unprivileged Users Edges to All Nodes |Find all the Edges that any UNPRIVILEGED user (based on the admincount:False) has against all the nodes |Ryan Hausknecht (@haus3c) |```MATCH (n:User {admincount:False}) MATCH (m) WHERE NOT m.name = n.name MATCH p=allShortestPaths((n)-[r:MemberOf|HasSession|AdminTo|AllExtendedRights|AddMember|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|CanRDP|ExecuteDCOM|AllowedToDelegate|ReadLAPSPassword|Contains|GpLink|AddAllowedToAct|AllowedToAct|SQLAdmin*1..]->(m)) RETURN p LIMIT 10```|
|2020/06/20 |Unprivileged Users ACL abusing other Users |Find interesting edges related to “ACL Abuse” that uprivileged users have against other users |Ryan Hausknecht (@haus3c) |```MATCH (n:User {admincount:False}) MATCH (m:User) WHERE NOT m.name = n.name MATCH p=allShortestPaths((n)-[r:AllExtendedRights|ForceChangePassword|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner*1..]->(m)) RETURN p```|
|2020/06/20 |Workstations a user can RDP into |Find workstations a user can RDP into. |Ryan Hausknecht (@haus3c) |```MATCH p=(g:Group)-[:CanRDP]->(c:Computer) WHERE g.objectid ENDS WITH '-513' AND NOT c.operatingsystem CONTAINS 'Server' RETURN p```|
|2020/06/20 |All Domain Users Edges Against all Computers |Find all the privileges (edges) of the domain users against the domain computers (e.g. CanRDP, AdminTo etc. HasSession edge is not included) |Ryan Hausknecht (@haus3c) |```MATCH p1=shortestPath(((u1:User)-[r1:MemberOf*1..]->(g1:Group))) MATCH p2=(u1)-[*1]->(c:Computer) RETURN p2```|
|2020/06/20 |All Logged In Admins |List of all logged in administrators |Walter.Legowski (@SadProcessor) |```MATCH  p=(a:Computer)-[r:HasSession]->(b:User)  WITH a,b,r  MATCH  p=shortestPath((b)-[:AdminTo|MemberOf*1..]->(a))  RETURN b,a,r```|
|2020/06/20 |All Domain Admins |List of all domain admins |Walter.Legowski (@SadProcessor) |```MATCH (n:Group) WHERE n.name =~ "(?i).*DOMAIN ADMINS.*" WITH n  MATCH (n)<-[r:MemberOf*1..]-(m)  RETURN n,r,m```|
|2020/06/20 |Computers with Unconstrained Delegation |Find all computers with Unconstrained Delegation |Ryan Hausknecht (@haus3c) |```MATCH (c:Computer {unconstraineddelegation:true}) RETURN c```|
|2020/06/20 |Unprivileged Users ACL abusing Computers |Find interesting edges related to “ACL Abuse” that unprivileged users have against computers |Ryan Hausknecht (@haus3c) |```MATCH (n:User {admincount:False}) MATCH p=allShortestPaths((n)-[r:AllExtendedRights|GenericAll|GenericWrite|Owns|WriteDacl|WriteOwner|AdminTo|CanRDP|ExecuteDCOM|ForceChangePassword*1..]->(m:Computer)) RETURN p```|
|2020/06/20 |User Sessions in a Specific Domain |Find all sessions any user in a specific domain has. |Ryan Hausknecht (@haus3c) |```MATCH p=(m:Computer)-[r:HasSession]->(n:User {domain: "CONTOSO.LOCAL"}) RETURN p```|
|2020/06/20 |Top 10 Users with Most Local Admin Rights |List of top 10 users with most local admin rights |Walter.Legowski (@SadProcessor) |```MATCH (n:User),(m:Computer),(n)-[r:AdminTo]->(m) WHERE NOT n.name STARTS WITH 'ANONYMOUS LOGON' AND NOT n.name='' WITH n, count(r) as rel_count ORDER BY rel_count desc  LIMIT 10  MATCH (m)<-[r:AdminTo]-(n)  RETURN n,r,m ```|
|2020/06/20 |View all GPOs |View all GPOs |Ryan Hausknecht (@haus3c) |```MATCH (n:GPO) RETURN n```|
|2020/06/20 |SPNs with keywords |Find SPNs with keywords (swap SQL with whatever) |Ryan Hausknecht (@haus3c) |```MATCH (u:User) WHERE ANY (x IN u.serviceprincipalnames WHERE toUpper(x) CONTAINS 'SQL') RETURN u.name```|
|2020/06/20 |Group with keywords |Find a group with keywords. E.g. SQL ADMINS or SQL 2017 ADMINS |Ryan Hausknecht (@haus3c) |```MATCH (g:Group) WHERE g.name =~ '(?i).SQL.ADMIN.*' RETURN g```|
|2020/06/20 |Kerberoastable Users with passwords last set > 5 years |Find All Users with an SPN/Find all Kerberoastable Users with passwords last set > 5 years ago |Ryan Hausknecht (@haus3c) |```MATCH (u:User) WHERE u.hasspn=true AND u.pwdlastset < (datetime().epochseconds - (1825 * 86400)) AND NOT u.pwdlastset IN [-1.0, 0.0] RETURN u.name, u.pwdlastset order by u.pwdlastset```|
|2020/06/20 |DA sessions not on a certain group |DA sessions not on a certain group (e.g. domain controllers). |Ryan Hausknecht (@haus3c) |```OPTIONAL MATCH (c:Computer)-[:MemberOf]->(t:Group) WHERE NOT t.name = 'DOMAIN CONTROLLERS@CONTOSO.LOCAL' WITH c as NonDC MATCH p=(NonDC)-[:HasSession]->(n:User)-[:MemberOf]->(g:Group {name:'DOMAIN ADMINS@CONTOSO.LOCAL'}) RETURN DISTINCT (n.name) as Username, COUNT(DISTINCT(NonDC)) as Connexions ORDER BY COUNT(DISTINCT(NonDC)) DESC   ```|