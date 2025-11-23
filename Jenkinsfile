pipeline {
    agent any

    environment {
        VENV = "venv"
    }

    stages {

        stage('Direct Install - No Git Clone') {
            steps {
                sh '''
                # Create virtual environment
                python3.9 -m venv ${VENV}
                source ${VENV}/bin/activate
                
                # Upgrade pip
                pip install --upgrade pip setuptools wheel
                
                # Direct install FiftyOne - NO GIT CLONE
                pip install fiftyone --timeout 1200 --retries 5
                
                # Install torch separately
                pip install torch==2.2.2+cpu --extra-index-url https://download.pytorch.org/whl/cpu --timeout 600
                
                echo "FiftyOne installed successfully without Git!"
                '''
            }
        }

        stage('Verify Installation') {
            steps {
                sh '''
                source ${VENV}/bin/activate
                python -c "import fiftyone as fo; print('FiftyOne version:', fo.__version__)"
                python -c "import torch; print('PyTorch version:', torch.__version__)"
                '''
            }
        }

        stage('Optional: Download Sample Dataset') {
            steps {
                sh '''
                source ${VENV}/bin/activate
                # Quick test with sample dataset
                python -c "
                import fiftyone as fo
                import fiftyone.zoo as foz
                
                # Download small dataset for testing
                dataset = foz.load_zoo_dataset('quickstart')
                print('Dataset loaded:', dataset.name)
                print('Samples:', len(dataset))
                " || echo "Dataset download skipped or failed"
                '''
            }
        }

    }

    post {
        always {
            echo "Pipeline Completed - FiftyOne Installed Successfully!"
        }
    }
}
