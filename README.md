# bluetoothfinal1
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using InTheHand.Devices.Bluetooth;
using InTheHand.Net.Bluetooth;
using InTheHand.Net.Sockets;
using System;
using System.Collections.Generic;

namespace HLK_Client
{
    class HLKBoard
    {
        public event HLKBoardEventHandler HLKBoardConnectionComplete;
        public delegate void HLKBoardEventHandler(object sender, HLKBoardEventArgs e);

        private BluetoothClient _bluetoothClient;
        private BluetoothComponent _bluetoothComponent;
        private List<BluetoothDeviceInfo> _inRangeBluetoothDevices;
        private BluetoothDeviceInfo _hlkBoardDevice;
        private EventHandler<BluetoothWin32AuthenticationEventArgs> _bluetoothAuthenticatorHandler;
        private BluetoothWin32Authentication _bluetoothAuthenticator;


        public HLKBoard()
        {
            _bluetoothClient = new BluetoothClient();
            _bluetoothComponent = new BluetoothComponent(_bluetoothClient);
            _inRangeBluetoothDevices = new List<BluetoothDeviceInfo>();
            _bluetoothAuthenticatorHandler = new EventHandler<BluetoothWin32AuthenticationEventArgs>(_bluetoothAutenticator_handlePairingRequest);
            _bluetoothAuthenticator = new BluetoothWin32Authentication(_bluetoothAuthenticatorHandler);

            _bluetoothComponent.DiscoverDevicesProgress += _bluetoothComponent_DiscoverDevicesProgress;
            _bluetoothComponent.DiscoverDevicesComplete += _bluetoothComponent_DiscoverDevicesComplete;
        }


        public void ConnectAsync()
        {
            _inRangeBluetoothDevices.Clear();
            _hlkBoardDevice = null;
            _bluetoothComponent.DiscoverDevicesAsync(255, true, true, true, false, null);
        }


        private void PairWithBoard()
        {
            Console.WriteLine("Pairing...");

            bool pairResult = BluetoothSecurity.PairRequest(_hlkBoardDevice.DeviceAddress, null);

            if (pairResult)
            {
                Console.WriteLine("Success");
            }

            else
            {
                Console.WriteLine("Fail"); // Instantly fails
            }
        }


        private void _bluetoothComponent_DiscoverDevicesProgress(object sender, DiscoverDevicesEventArgs e)
        {
            _inRangeBluetoothDevices.AddRange(e.Devices);
        }

        private void _bluetoothComponent_DiscoverDevicesComplete(object sender, DiscoverDevicesEventArgs e)
        {
            for (int i = 0; i < _inRangeBluetoothDevices.Count; ++i)
            {
                if (_inRangeBluetoothDevices[i].DeviceName == "HLK")
                {
                    _hlkBoardDevice = _inRangeBluetoothDevices[i];
                    PairWithBoard();

                    return;
                }
            }

            HLKBoardConnectionComplete(this, new HLKBoardEventArgs(false, "Didn't found any \"HLK\" discoverable device"));
        }

        private void _bluetoothAutenticator_handlePairingRequest(object sender, BluetoothWin32AuthenticationEventArgs e)
        {
            e.Confirm = true; // Never reach this line
        }
    }
}
namespace bluetooth1
{

    class Program
    {
        static void Main(string[] args)
        {
            //BluetoothClient bc = new BluetoothClient();       
        }
    }
}
