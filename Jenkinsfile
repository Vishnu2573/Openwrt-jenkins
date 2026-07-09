pipeline {
    agent any

    stages {

        stage('Verify Build Environment') {
            steps {
                echo "Verifying build dependencies"

                sh '''
                    gcc --version
                    g++ --version
                    git --version
                    python3 --version
                    make --version
                '''
            }
        }

        stage('Clone OpenWrt Repository') {
            steps {

                sh '''
                    if [ ! -d openwrt ]; then
                        git clone https://github.com/openwrt/openwrt.git
                    fi
                '''
            }
        }

        stage('Verify Repository') {
            steps {

                dir('openwrt') {

                    sh '''
                        pwd
                        git branch
                        git log --oneline -5
                        ls
                        ls Makefile
                    '''
                }
            }
        }

        stage('Checkout Stable Branch') {
            steps {

                dir('openwrt') {

                    sh '''
                        git fetch --all
                        git checkout openwrt-25.12
                        git branch
                    '''
                }
            }
        }

        stage('Verify OpenWrt Build System') {
            steps {

                dir('openwrt') {

                    sh '''
                        ls
                        cat feeds.conf.default
                    '''
                }
            }
        }

        stage('Update Feeds') {
            steps {

                dir('openwrt') {

                    sh '''
                        ./scripts/feeds update -a
                        ./scripts/feeds install -a
                        ls feeds
                        ls -l package/feeds/packages | head
                    '''
                }
            }
        }

        stage('Configure OpenWrt') {
            steps {

                echo '''
Interactive configuration (make menuconfig)
is not suitable for Jenkins.

In a real CI pipeline, a pre-generated .config
file should be copied and used with:

make defconfig
'''
            }
        }

        stage('Build OpenWrt') {
            steps {

                dir('openwrt') {

                    sh '''
                        make -j$(nproc) V=s
                    '''
                }
            }
        }
    }
}
