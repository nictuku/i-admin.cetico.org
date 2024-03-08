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
# Archived Comments

#### Pessoal,  
Vejam tambem os serviços da Smart Unio...
[Anonymous]( "noreply@blogger.com") - <time datetime="2006-06-11T23:23:00.000-07:00">Jun 1, 2006</time>

Pessoal,  
Vejam tambem os serviços da Smart Union, pois lá eu vi um serviço de monitoramento interessante. Veja o no link abaixo (copie e cole em seu browser):  
  
http://www.smartunion.com.br/monitoramento\_automatizado\_servidores\_via\_internet.asp  
  
Abraços,  
  
João Pedrosa dos Santos  
Chief Technology Officer  
Embratel
<hr />
#### vai disponibilizar o codigo?
[Anonymous]( "noreply@blogger.com") - <time datetime="2009-08-04T14:51:13.986-07:00">Aug 2, 2009</time>

vai disponibilizar o codigo?
<hr />
#### nagios 2.o is way old! check out nagios 3.2.1 with...
[Anonymous]( "noreply@blogger.com") - <time datetime="2010-03-29T18:25:05.796-07:00">Mar 2, 2010</time>

nagios 2.o is way old!  
check out nagios 3.2.1 with centreon and nagvis  
i have a how to on my site  
www.felipeferreira.net
<hr />
