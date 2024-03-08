---
title: 'Sistema de monitoramento de serviços e servidores'
date: 2005-05-23T05:11:00.000-07:00
draft: false
url: /2005/05/sistema-de-monitoramento-de-servios-e.html
---

Monitorar o status de serviços e servidores de TI é uma tarefa complexa, mas bastante importante.  
  
Para um administrador - pelo menos para mim - ser o último a saber que um serviço saiu do ar é extremamente constrangedor. Com essa preocupação, planejei um sistema de monitoramento de serviços que fosse confiável e flexível. O primeiro desafio, entretanto, seria adaptar a feia interface do Nagios a um sistema mais "visual" e intuitivo.  
  
Uma interface mais simples, flexível e bonita. Se tudo corresse como planejado, no final do processo poderíamos até utilizar equipamentos de monitoramento, à vista de todos no prédio da administração da empresa. A idéia era dar maior transparência na adminstração de TI, porém sem deixar de lado os cuidados com a segurança de informações.  
  
Utilizei a nova árvore 2.0, ainda beta, do nagios, para que o projeto pudesse acompanhar melhor as futuras versões desse software.  
  
Para isso, foi necessário desenvolver um sistema de "conexão" do nagios 2.0 com o mysql, visto que não havia nada pronto nesse sentido, quando o projeto foi iniciado.  
  
Um processo daemon é utilizado para monitorar as verificações do Nagios, e atualizar o banco de dados com as informações necessárias.  
  
Além disso, há grande disponibilidade de plugins para monitoramento de serviços e servidores. Havia a opção inclusive de utilizar softwares que fizessem uma maior integração com os servidores Windows, inclusive via WMI, mas preferi não aumentar a complexidade da estrutura já existente.  
  
Veja os resultados:  
  
[![Free Image Hosting at www.ImageShack.us](http://img229.echo.cx/img229/8452/nagiosui2wk.th.jpg)](http://img229.echo.cx/my.php?image=nagiosui2wk.jpg) \- Interface para o Nagios, utilizando ícones funcionais do MS Visual Studio. (utilizando em Full-screen, mas mostrando barras do IE apenas para contextualização)  
  
  
[![Free Image Hosting at www.ImageShack.us](http://img267.echo.cx/img267/5052/nagiosmon4lt.th.jpg)](http://img267.echo.cx/my.php?image=nagiosmon4lt.jpg)\- Ao fundo, monitores mostram o status de serviços essenciais de TI.