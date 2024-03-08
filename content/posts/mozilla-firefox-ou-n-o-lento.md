---
title: 'Mozilla (Firefox ou não) lento'
date: 2005-09-17T14:35:00.000-07:00
draft: true
url: 
---

Eu rodo um terminal de monitoramento de servidores com navegador Mozilla. Essa máquina tem dois monitores que mostram a disponibilidade dos recursos de rede (usando minha interface pro Nagios), nos quais aparecem telas alternadas com o status dos serviços.  
  
Um problema chato é que o Mozilla, depois de estar rodando por algum dias, passa a usar todos os recursos do sistema - nominalmente memória e processador.  
  
Uma possível correção seria definir o tamanho do cache de memória e de disco.  
  
Acesse a página "about:config" e altere as opções browser.cache.memory.size para um valor sensato para sua máquina. Tente por exemplo 30000, que reservará 30MB.
