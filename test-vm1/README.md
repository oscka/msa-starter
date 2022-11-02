# Vagrant

Vagrant는 Harshicorp사에서 만들어진 개발용 가상환경 Bootstrap도구입니다. 디렉토리 단위로 관리되며 사전에 가상환경 provider(Hypervisor라고도 하며 VirtualBox,Hyper-v,vmware,libvirt 등)와 vagrant만 설치되어 있으면 간단하게 Vagrantfile만 정의하여 가상 서버를 시작할 수 있습니다. 

개발자들에 의해 많이 사용되어 VirtualBox, vmware등이 provider로 선택되며 linux 기반일 경우 kvm(libvirt)등을 사용하는 것이 성능이나 속도 면에서 월등히 좋습니다.


VirtualBox 기반의 설정은 다음과 같습니다. VMHOSTNAME, VMIP는 넣는 값이 구동되는 vm의 값이 됩니다.

미리 box(ubuntu/focal64)를 받아 두어야 하며, 받지 않았을 경우 vagrant up시 다운로드 됩니다.

```
VMHOSTNAME="test-vm1"
VMIP="192.168.56.10"

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.hostname = VMHOSTNAME
  config.vm.network :private_network, ip: VMIP 
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
      vb.gui = false
      vb.name = VMHOSTNAME
      vb.cpus = 4 
      vb.memory = "8192"
  end
end
```

libvirt환경의 경우 Vagrant-libvirt파일을 받아 사용합니다. 여러개의 provider를 동시에 사용할 수는 없습니다.