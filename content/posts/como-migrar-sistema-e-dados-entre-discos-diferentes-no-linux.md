---
title: 'Como migrar sistema e dados entre discos diferentes no Linux'
date: 2005-09-17T11:29:00.000-07:00
draft: true
url: 
---

Para migrar os dados e o sistema entre discos diferentes no Linux, o método mais simples é o seguinte.  
  
Coloque o HD de DESTINO no sistema de origem, como secundário.  
  
Boote o sistema.  
  
Crie as partições necessárias, usando por exemplo o cfdisk. Depois, monte o sistema de arquivos de destino:  
  
\# mount -t ext3 /dev/hdb1 /mnt/destino  
  
Agora, copie o sistema e os dados do HD de origem. Supondo que você tenha apenas um HD (ou partição lógica, caso use RAID ou LVM), utilize o seguinte comando:  
  
\# cp -v -a -x / /mnt/destino  
  
Esse comando copiará todos os dados para o novo HD, preservando links simbólicos (symlinks) e todas as permissões.  
  
Caso você tenha outra partição no sistema de origem (exemplo, /boot, /var), você deve o mesmo procedimento para cada caso. Se no sistema destino você estiver usando uma partição separada para isso, monte-a e depois faça a cópia:  
  
\# mkdir /mnt/destino\_boot  
  
\# mount -t ext2 /dev/hdb2 /mnt/destino\_boot  
  
\# cp -v -a -x /boot /mnt/destino\_boot  
  
Depois, é só reinstalar o grub no disco de destino. Uma forma interessante de fazer isso é:  
  
\# dd if=/dev/hda of=/dev/hdb bs=512 count=1  
  
Esse comando copiará a partição de boot para o disco novo. A posição dos blocos dos arquivos de boot lidos pelo grub são diferentes. Isso não funcionaria no LILO, por exemplo.  
  
Não se esqueça também de editar o fstab do sistema de destino, de forma que esse esteja de acordo o novo layout das partições. Note que, se atualmente o disco está aparecendo como /dev/hdb, no futuro, quando ele for colocado como hd principal ele se chamará /dev/hda.