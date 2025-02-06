node { // Memulai pipeline Jenkins baru
 stage('Build') { // Mendefinisikan tahap Build
     docker.image('python:3.12.1-alpine3.19').inside { // Menggunakan image Docker 'python:3.12.1-alpine3.19'.
         sh 'python -m py_compile sources/add2vals.py sources/calc.py' // Menjalankan perintah shell untuk mengkompilasi file Python 'add2vals.py' dan 'calc.py' menggunakan py_compile.
     }
 }

 stage('Test') { // Mendefinisikan tahap Test
     docker.image('qnib/pytest').inside { // Menggunakan image Docker 'qnib/pytest'.
         sh 'py.test --junit-xml test-reports/results.xml sources/test_calc.py' // Menjalankan pengujian menggunakan pytest dan menghasilkan laporan dalam format JUnit XML yang disimpan di 'test-reports/results.xml'.
     }
     junit 'test-reports/results.xml' // Menyediakan laporan hasil pengujian yang telah dihasilkan di 'test-reports/results.xml' dalam bentuk JUnit.
 }

 stage('Manual Approval') { // Mendefinisikan tahap Manual Approval
     input message: 'Lanjutkan ke tahap Deploy?', ok: 'Proceed' // Menunggu persetujuan manual untuk melanjutkan ke tahap Deploy
 }

 stage('Deploy') { // Mendefinisikan tahap Deploy
     withEnv(["VOLUME=" + pwd() + "/sources:/src", "IMAGE=cdrx/pyinstaller-linux:python2"]) { // Mengatur variabel lingkungan VOLUME dan IMAGE untuk menjalankan perintah Docker dengan volume yang sesuai dan image untuk PyInstaller.
         dir(env.BUILD_ID) { // Memastikan berada di direktori build yang sesuai
             sh "docker run --rm -v ${VOLUME} ${IMAGE} 'pyinstaller -F add2vals.py'" // Menjalankan perintah Docker untuk membangun executable dari 'add2vals.py' menggunakan PyInstaller.
         }
         archiveArtifacts "sources/dist/add2vals" // Mengarsipkan hasil artefak yang dibangun di direktori 'sources/dist/add2vals'.
         sh "docker run --rm -v ${VOLUME} ${IMAGE} 'rm -rf build dist'" // Membersihkan direktori build dan dist di dalam container Docker
     }
     sleep 60 // Menunggu selama 60 detik
 }
}
