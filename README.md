This repo contains instructions and scripts for provisioning a Raspberry Pi based music box. The music box will use mopidy and spotify to emit music via the Pi's headphone jack.

# Installation instructions

1. Download the latest 'Raspbian stretch lite' image from `https://www.raspberrypi.org/downloads/raspbian/` .
2. Flash the image you downloaded to a SD card and boot your Pi with it.
3. Edit the file ansible/inventory.yml in this repo following the instructions in the comments.
4. Install ansible if you don't have it already.
5. SSH in to your Pi to ensure the SSH connection works. You may need to use the instructions here: https://www.raspberrypi.org/documentation/remote-access/ssh/
6. Run `ansible-playbook -i ansible/inventory.yml ansible/setup.yml`
7. Access your Pi over http (e.g. http://music.local/) and you should be good to go.

# Updating image locally before flashing

If you want to set up or test an image locally before flashing it you can use the following instructions. Be aware that as Qemu is emulating an ARM CPU, it's going to run quite slowly.

1. Download Qemu for ARM to your machine (it will provide the qemu-system-arm binary).
2. Download the latest kernel from here: `https://github.com/dhruvvyas90/qemu-rpi-kernel/` . I chose `kernel-qemu-4.4.34-jessie` (it worked fine with stretch).
3. Follow the instructions here to prepare your image for Qemu: https://github.com/dhruvvyas90/qemu-rpi-kernel/wiki/Emulating-Jessie-image-with-4.x.xx-kernel
4. Run the following line, replacing <kernel> with your kernel file, and <image> with your Raspbian image. Note the hostfwd entries which will forward ports 22->2222, 6680->6680 and 80->8080 :

    qemu-system-arm -kernel <kernel> -cpu arm1176 -m 256 -M versatilepb -net nic -net user,hostfwd=tcp::2222-:22,hostfwd=tcp::6680-:6680,hostfwd=tcp::8080-:80 -no-reboot -drive format=raw,file=<image> -append "root=/dev/sda2 panic=1"

5. Once that's booted successfully, if you want to connect via SSH, you'll need to follow these instructions: https://www.raspberrypi.org/documentation/remote-access/ssh/

Once you've got that up, your pi can be SSH-ed into by running: `ssh -p 2222 pi@localhost` and entering the password 'raspberry'. You can use the ansible scripts to provision this machine, and the image will be updated.

You can flash the updated .img file to the Pi, but just remember to remount it and uncomment the entries you commented out as part of step 2.
