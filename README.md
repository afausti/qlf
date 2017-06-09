## DESI Quick Look Framework

See development documentation at http://quick-look-framework.readthedocs.io

### Install DESI QLF locally

1. Install the Quick Look Framework (first time installation)

    ```
    export QLF_ROOT=$HOME/quicklook
    mkdir -p $QLF_ROOT
    cd $QLF_ROOT
   
    git clone https://github.com/linea-it/qlf.git
    ```

2. Install software dependencies (first time installation)

    ```
    source ~/miniconda3/bin/activate
    ```
    
    NOTE: miniconda3 can be installed from https://conda.io/docs/install/quick.html
   
    ```
    conda config --add channels conda-forge
    conda create --name quicklook python=3.5 --yes --file qlf/requirements.txt
    source activate quicklook
    # Packages not available through conda
    pip install -r qlf/extras.txt
    ```

3. Install the DESI Quick Look pipeline (first time installation)

    ```
    cd $QLF_ROOT
    git clone https://github.com/desihub/desispec.git
    git clone https://github.com/desihub/desiutil.git
  
    for package in desispec desiutil; do
        echo "Setting $package..."
        export PATH=$QLF_ROOT/$package/bin:$PATH
        export PYTHONPATH=$QLF_ROOT/$package/py:$PYTHONPATH
    done
    ```
    
    At this point the DESI Quick Look pipeline should be available in your terminal
    
    ```
    desi_quicklook --help
    ```

4. Get some test data

    ```
    export DESI_SPECTRO_DATA=$HOME/data
    mkdir -p $DESI_SPECTRO_DATA
    cd $DESI_SPECTRO_DATA
    
    # Test data for local run of Quick Look, night 20170428, exposures 3,4 and cameras r0,b0
    http://portal.nersc.gov/project/desi/data/quicklook/
    ```
    
    NOTE: on desidev server, you migth copy ~1 night of data from `/home/angelofausti/data/20170428.tgz`

5. Update the `qlf.cfg` file

    ```
    cd $QLF_ROOT/qlf/config
    cp qlf.cfg.template qlf.cfg
    
    Update the qlf.cfg file with local paths for the input data, log files, etc.
    ```
    
    NOTE: in development mode, QLF will process the data specified in the `qlf.cfg`. Each time you run QLF a fresh database is created and the processing results are ingested at the end.

6. Change sqlite3 to postgresql (optional)

    If you want to use the postgres database, do the following:
    (Assuming the postgres server is installed and running)

    ```
    CREATE USER <db_user> WITH PASSWORD '<password>';
    CREATE DATABASE <db_project> owner <db_user>;
    ```

    Now install the PostgreSQL database adapter:
    ```
    pip install psycopg2
    ```

    And edit the $QLF_ROOT/qlf/qlf/settings.py file by changing the DATABASES variable:

    ```
    DATABASES = {
         'Default': {
                 'ENGINE': 'django.db.backends.postgresql_psycopg2',
                 'NAME': '<db_project>',
                 'USER': '<db_user>',
                 'PASSWORD': '<password>',
                 'HOST': '127.0.0.1',
                 'PORT': '',
             }
    }
    ```

    NOTE: Changing sqlite3 by postgresql is not recommended for the development mode because the database is not recreated.


7. Start the QLF application 

    ```
    source ~/miniconda3/bin/activate
    export QLF_ROOT=$HOME/quicklook
    cd $QLF_ROOT/qlf/qlf
    ./run.sh
    ```
    
    NOTE: the log file for the QLF execution is specified at `qlf.cfg`, you can follow the progress of data processing from there, or monitor the processes running wih `ps ux` or use the Monitor interface in the Quick Look web application. The Quick Look web application runs at `http://localhost:8000`

    NOTE: Make sure you don't have old QLF processes running when you start the QLF application, process management will improve in the next versions.

