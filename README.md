python3-moos
===========

python3 bindings for MOOS

Build instructions
==================
    mkdir -p build
    cd build
    cmake ..
    make
    sudo make install

Usage
=====
Examples can be found in the Documentation/examples folder.

### Example 1: simplecomms.py
    import pymoos
    import time

    # A super simple example:
    # a) we make a comms object, and run it
    # b) we enter a forever loop in which we both send notifications
    #    and check and print mail (with fetch)
    # c) the map(lambda,,,) bit simply applies trace() to every message to print
    #    a summary of the message.

    comms = pymoos.comms()

    def on_connect():
        return comms.register('simple_var',0)

    def main():
        # my code here
        comms.set_on_connect_callback(on_connect);
        comms.run('localhost',9000,'pymoos')
        
        while True:
            time.sleep(1)
            comms.notify('simple_var','hello world',pymoos.time());
            map(lambda msg: msg.trace(), comms.fetch())

    if __name__ == "__main__":
        main()        

### Example 2: GenPythonMOOSApp script
The GenPythonMOOSApp script included in the Documentation folder can create an outline of a python MOOSApp. The python MOOSApp can be started by pAntler, and reads configuration blocks from the .moos file used with pAntler.

Make a new python MOOSApp:
    ./GenPythonMOOSApp name prefix

Run app with:

    ./MOOSApp.py [moos-config-file.moos] [Alias]
    
or

    ./MOOSApp.py [moos-config-file.moos]
    
or

    ./MOOSApp.py
