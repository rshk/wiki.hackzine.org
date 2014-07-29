Hyper-V: Create VM from PowerShell
##################################

I needed to create a cluster of (identical) VMs on an Hyper-V cluster.

To speed up things, I created a PowerShell script to automatically
deploy VMs, cloning a template virtual harddisk.

.. code-block:: powershell

    # ------------------------------------------------------------
    # Create Hyper-V virtual machine via PowerShell
    # ------------------------------------------------------------

    # Variables
    $VM_NAME = "PS_TEST_VM"
    $VM_MAC = "00:15:5d:11:22:01"

    $VM_RAM = 256GB
    $VM_CPUS = 16
    $VM_DEST_PATH = "C:\ClusterStorage\Volume1\tests"
    $VM_VLAN = 82
    $VM_HOST = "HYPERV_NODE_25"
    $NETWORK_SWITCH = "DefaultNetwork"
    $ROOT_VHD_TPL = "C:\ClusterStorage\Volume1\tests\Template.vhdx"
    $VM_ROOT_VHD = "${VM_DEST_PATH}/${VM_NAME}.vhdx"
    $VM_DATA_VHD = "${VM_DEST_PATH}/${VM_NAME}-data.vhdx"
    $VM_DATA_VHD_SIZE = 80GB
    $VM_CLUSTER = "HV_CLUSTER_05"

    # Create destination folder for VMs
    MD $VM_DEST_PATH -ErrorAction SilentlyContinue

    # Create Virtual Machine
    # ------------------------------------------------------------

    echo "Creating VM: $VM_NAME on ${VM_HOST}"
    $VM = New-VM -Name $VM_NAME -Path $VM_DEST_PATH -MemoryStartupBytes $VM_RAM -ComputerName $VM_HOST
    #echo "    VM Id: $( $VM.Id )"
    #echo "    VM Name: $( $VM.Name )"
    #echo "    Host: $( $VM.ComputerName )"

    # Create & attach VHDs
    # ------------------------------------------------------------
    # Note: it is impossible to boot from SCSI controllers

    echo "Getting first SCSI controller"

    echo "Copying Root VHD to $VM_ROOT_VHD"
    Convert-VHD -Path $ROOT_VHD_TPL -DestinationPath $VM_ROOT_VHD
    Add-VMHardDiskDrive -VM $VM -ControllerType IDE -ControllerNumber 0 -ControllerLocation 0 -Path $VM_ROOT_VHD

    echo "Creating new Data disk $VM_DATA_VHD_SIZE in $VM_DATA_VHD"
    New-VHD -Path $VM_DATA_VHD -SizeBytes $VM_DATA_VHD_SIZE -Dynamic
    Add-VMHardDiskDrive -VM $VM -ControllerType IDE -ControllerNumber 0 -ControllerLocation 1 -Path $VM_DATA_VHD

    # Set CPU / memory
    # ------------------------------------------------------------

    echo "Setting CPU / Memory configuration"
    Set-VMProcessor -VM $VM -Count $VM_CPUS
    Set-VMMemory -VM $VM -DynamicMemoryEnabled $True -MaximumBytes 512GB -MinimumBytes 16GB -StartupBytes $VM_RAM

    # Configure NIC
    # ------------------------------------------------------------

    echo "Configuring NIC (mac address: ${VM_MAC}, switch: ${NETWORK_SWITCH})"
    Remove-VMNetworkAdapter -VM $VM
    Add-VMNetworkAdapter -VM $VM -Name PublicNIC -StaticMacAddress $VM_MAC -SwitchName $NETWORK_SWITCH
    $ADAPTER = Get-VMNetworkAdapter -VM $VM
    Set-VMNetworkAdapterVlan -VMNetworkAdapter $ADAPTER -Access -VlanId $VM_VLAN
    # Connect-VMNetworkAdapter -VMNetworkAdapter $ADAPTER -SwitchName $NETWORK_SWITCH

    # Add VM to cluster
    # ------------------------------------------------------------

    echo "Adding VM to cluster"
    Add-ClusterVirtualMachineRole -VMName $VM_NAME -Cluster $VM_CLUSTER

    # Start VM
    # ------------------------------------------------------------

    #Start-VM $VM_NAME


Todo-List
=========

- Add more checks to make the script more robust, in case the default
  VM settings should change
- Make this script accept arguments some way to directly create a
  batch of VMs without having to manually change configuration
  variables
- Figure out some way to perform unattended finalization of VM
  installation

  - Change passwords
  - Setup SSH server
  - Install + register Saltstack + issue highstate
  - (This probably could be done via PXE boot -- but we need a legacy NIC..)
