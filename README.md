# Multilabel classification on Stack Overflow tags
Predict tags for posts from StackOverflow with multilabel classification approach.


## Kubernetes instruction 

To initialize the cluster we have to: 

1. Build dockerfile within src/redirecting_service and name this build: remla-redirecting-service:latest (later we will replace with online build version but for testing purposes this is easier).
2. Run `kubectl apply -f .\k8s-local-ingress-controller.yaml` building the ingress controller
3. Run `kubectl apply -f .\k8s-local-deployment.yaml`
4. Forward the port to the host: `kubectl port-forward --namespace=ingress-nginx service/ingress-nginx-controller 8080:80 `
5. redirecting service exposed on: http://remla.localdev.me


Minikube 
1. `minikube addons enable ingress`
2. `kubectl apply -f .\k8s-local-deployment.yaml`
3. Forward the port to the host: `kubectl port-forward --namespace=ingress-nginx service/ingress-nginx-controller 8080:80 `





Cleanup: To remove old model deployments: `kubectl delete all --all`

## Instruction of using the app
- Go to `http://remla.localdev.me/admin` Input a version of a model you want to deploy. Version should be >= 1.6.1
- Wait until the model service deploys to the cluster successfully (Better manually check)
- Refresh the page. The dropdown should contain a list of model. If there is only one model, the model gets active 
automatically. The active model is the one in production. The rest models are shadow models.
- Select a model you want to release in the dropdown and press `Set Active`
- Go to `http://remla.localdev.me`. Input description and press predict to get some tag recommendation!
- On the given result. You can write your tag improvement feedback to the provided input field.
- Go to `http://remla.localdev.me/prometheus` to check current metrics. Model metrics could be found by filtering with 
'model' in status -> targets.

## Dataset
- Dataset of post titles from StackOverflow

## Transforming text to a vector
- Transformed text data to numeric vectors using bag-of-words and TF-IDF.

## MultiLabel classifier
[MultiLabelBinarizer](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MultiLabelBinarizer.html) to transform labels in a binary form and the prediction will be a mask of 0s and 1s.

[Logistic Regression](http://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html) for Multilabel classification
- Coefficient = 10
- L2-regularization technique

## Evaluation
Results evaluated using several classification metrics:
- [Accuracy](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)
- [F1-score](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)
- [Area under ROC-curve](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)
- [Area under precision-recall curve](http://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html#sklearn.metrics.average_precision_score)

## Libraries
- [Numpy](http://www.numpy.org/) — a package for scientific computing.
- [Pandas](https://pandas.pydata.org/) — a library providing high-performance, easy-to-use data structures and data analysis tools for the Python
- [scikit-learn](http://scikit-learn.org/stable/index.html) — a tool for data mining and data analysis.
- [NLTK](http://www.nltk.org/) — a platform to work with natural language.

## DVC
Everything in the ```data/``` directory is tracked by DVC.

## Docker
Dockerfiles are found in the docker folder. Note that the build context should be the root project folder, and not the folder the dockerfile is contained in.

To build the inference API:
`docker build -f docker/inference-api/Dockerfile -t inference-api .`

To build the redirecting service:
`docker build -f docker/redirecting-service/Dockerfile -t redirecting-service .`

<hr>
Note: this sample project was originally created by @partoftheorigin
