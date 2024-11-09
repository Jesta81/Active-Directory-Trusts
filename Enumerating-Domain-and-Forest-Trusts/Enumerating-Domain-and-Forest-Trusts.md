## Enumerating Domain & Forest Trusts 

- When performing initial Active Directory enumeration, it is important to note down any and all trust relationships, as trusts may present an attack vector that may lead to compromise of one or more domains through either misconfigurations or abusing built-in functionality. 
- We can use various tools to enumerate domain and forest trust relationships. We'll cover an example using a built-in cmdlet as well as an open source tool. If we land on a machine with the [ActiveDirectory PowerShell module](https://learn.microsoft.com/en-us/powershell/module/activedirectory/?view=windowsserver2022-ps) available we can use the Get-ADTrust cmdlet to enumerate any possible trust relationships. 


	PS C:\> Import-Module activedirectory
	PS C:\> Get-ADTrust -Filter *


![AD-Trust](/Enumerating-Domain-and-Forest-Trusts/images/AD-Trust.png) 


- From the output we can see that we have a trust with the LOGISTICS.AD domain from our current position in the INLANEFREIGHT.AD domain. The trust is bidirectional (or two-way), meaning that users and computers in the INLANEFREIGHT.AD domain can likely access resources in the LOGISTICS.AD domain, and vice-versa. 

- This trust is between two forests, which we will see more of in the sections about cross forest trust attacks. We also have a bidirectional trust with the child domain CHILD.INLANEFREIGHT.AD. 

- The trust is intra-forest (meaning that both domains belong to the same forest), so we can safely assume that we are currently positioned in the domain within the INLANEFREIGHT.AD forest. This is important because it will play an important part in the types of attacks we are able to perform later on in this module. 

- Next, let's perform the same enumeration using an old (but still great) tool, PowerView. This tool comes equipped with several functions for enumerating domain trusts, documented [here](https://powersploit.readthedocs.io/en/latest/Recon/?q=Trusts&check_keywords=yes&area=default#domain-trust-functions). To start out let's use the **Get-DomainTrust** function and see what information is returned. 


![AD-Trust](/Enumerating-Domain-and-Forest-Trusts/images/Domain-Trust.png) 


- This is similar to the output given by the **Get-ADTrust** cmdlet but presented in a more user friendly manner. We can quickly see that we are indeed in the parent domain, INLANEFREIGHT.AD and we have a cross forest trust with the LOGISTICS.AD domain and an intra forest trust with the CHILD.INLANEFREIGHT.AD domain. The **Get-DomainTrustMapping** function is useful as well. 


![AD-Trust](/Enumerating-Domain-and-Forest-Trusts/images/mapping.png) 


- This function will first perform the same enumeration as the **Get-DomainTrust** function and then attempt to enumerate all trusts for every domain that is uncovered. In the above output we see what we already know, our current position and the child trust and bidirectional forest trust enumerated earlier. 

- We also see another forest trust between the LOGISTICS.AD and MEGACORP.AD forests. This is an outbound trust relationship, meaning that users and computers in the MEGACORP.AD domain may be able to access resources in the LOGISTICS.AD domain. 

- It is worth noting down as we could possibly find a way into the MEGACORP.AD domain (i.e. credential reuse, etc.), however cross-forest trust attacks that would require a user in the LOGISTICS domain to be able to authenticate into the MEGACORP domain will not work. 
