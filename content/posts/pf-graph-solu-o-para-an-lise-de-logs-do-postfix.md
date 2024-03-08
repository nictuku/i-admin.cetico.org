---
title: 'PF-Graph - Solução para análise de logs do Postfix'
date: 2005-06-07T07:40:00.000-07:00
draft: false
url: /2005/06/pf-graph-soluo-para-anlise-de-logs-do.html
---

Estou desenvolvendo uma ferramenta para análise de logs do Postfix. O PF-Graph, além de gerar relatórios com base em remetentes ou destinatários (incluíndo busca por domínios), cria gráficos coloridos sobre o status do serviço.  
  
Veja abaixo algumas screenshots:  
  
[![Free Image Hosting at www.ImageShack.us](http://img212.echo.cx/img212/2430/pfgraph9lj.th.png)PF-Graph, mostrando o sumário de gráficos.](http://img212.echo.cx/my.php?image=pfgraph9lj.png)  
  
  
  
[![Free Image Hosting at www.ImageShack.us](http://img279.echo.cx/img279/4962/pfgraph23dm.th.png)Resultado de busca](http://img279.echo.cx/my.php?image=pfgraph23dm.png)  
  
Entre os recursos do PF-Graph, incluem-se:  
  
\- Capacidade de auditar todas as mensagens recebidas e enviadas em um ou mais servidor de e-mail, em tempo real.  
  
\- Um daemon/agente rodando nos servidores de e-mail é responsável por coletar as informações em tempo real, e atualizar o banco de dados.  
  
\- Capacidade de mostrar total de bytes trafegados para um usuário, domínio ou qualquer termo de busca(exige Amavisd-new ou Suriproxy).  
  
\- Escrita em Perl, com integração nativa para MySQL, mas possível de se adaptar para outros bancos de dados, conforme a necessidade.  
  
  
Assim que eu der uma "limpada" no código do PF-Graph, o mesmo será disponibilizado sob a licença GPL. Caso esteja interessado nessa ferramenta, deixe um comentário!  
  
Até mais!