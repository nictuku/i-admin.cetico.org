---
title: 'Suriproxy -  Release soon'
date: 2005-09-08T15:15:00.001-07:00
draft: false
url: /2005/09/suriproxy-release-soon.html
---

"Release Soon, Release Often".  
  
Com esse mantra na cabeça, estou disponibilizando o Suriproxy, uma ferramenta anti-spam e, principalmente, anti-phishing.  
  
O suriproxy é um PROXY SMTPD para o Postfix, que filtra as mensagens  
recebidas pelo daemon smtpd e repassa a outro daemon, seja um amavis ou outro  
smtpd do postfix.  
  
Ele faz consultas URIBL de DNS quando encontra algum link nas mensagens,  
ele consulta se o domínio deste link está nas listas URIBL (inclusive na  
uribl.cetico.com).  
  
Veja o exemplo. Você recebe o e-mail com o seguinte texto:  
  
<a href="http://www.blable.net">CLIQUE AQUI</a>  
  
ou ainda  
  
<a href="http://www.blable.net/virus-mal.scr">CLIQUE AQUI</a>  
  
O Postfix pedirá que o Suriproxy verifique essa mensagem. Este, por sua vez,  
irá consultar seus dominios URIBL (uribl.cetico.com e multi.surbl.org),  
para ver se "blable.net" está em alguma dessas listas.  
  
Caso esteja, antes mesmo de se terminar a conexão SMTP, o Postfix mostrará  
uma mensagem de erro, informando que aquele e-mail não foi aceito. Isso  
evita que mensagens sejam perdidas, mas por outro lado não gera BOUNCES ou  
tráfego desnecessário para você.  
  
  
A instalação pode ser um pouco complexa para usuários iniciantes, principalmente porque ainda não tive tempo de escrever uma documentação bem detalhada.  
  
Além disso, em alguns casos, um bug ainda não resovido faz com que a ferramenta aja de forma inesperada, fechando conexões antes da hora, o que faz com que o servidor remoto tenha que enviar a mensagem novamente. Isso geralmente só acontece quando o host remoto envia mensagens lentamente.  
  
Para pegar o Suriproxy, [siga este link](http://www.cetico.org/suriproxy-0.13.tar.gz). Veja o README sobre instruções de instalação. Qualquer dúvida deixe um comentário aqui, que responderei assim que possível.  
  
Muito obrigado a todos pelo interesse :-).  
  
UPDATE: código do [Suriproxy](http://github.com/nictuku/suriproxy) no github, caso seja útil pra alguém.