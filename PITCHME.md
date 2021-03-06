
## Django REST Framework

---

### Requirements

- Python (2.7 - 3.6)
- Django==2.0.8 (1.10, 1.11, 2.0)

---

### Installation
```python
pip install djangorestframework
```
---

### Settings

```python
INSTALLED_APPS = (
     ... 
     'rest_framework', 
 )
```
---
- serializers.py
- urls.py
- views.py
---

### Serialization

- Mit csinál?
    - Összetett adatok --> JSON
    - Deserialization
        - Validálás
        - JSON --> összetett adat

---

### Serializers

- BaseSerializer
    - ősosztály
- Serializer
- ModelSerializer

---

### Serializer

- Mezők létrehozása 
- Implementálni kell
    - create
    - update

---

#### Serializer

```python
class Task(models.Model):
    title = models.CharField(max_length=150)
    text = models.TextField()
```
```python
class TaskSerializer(serializers.Serializer):
    title = serializers.CharField(
        required=False, allow_blank=True, max_length=100)
    text = serializers.CharField()
    
    def create(self, validated_data):
        return Comment.objects.create(**validated_data)
    
    def update(self, insatnce, validated_data):
        instance.title = 
                validated_data.get('title', instance.title)
        instance.text = 
                validated_data.get('text', instance.text)
        return instance
```
---

### ModelSerializer
- Mezők generálása a modell alapján
- No magic
    - update, create implementálva

---

#### ModelSerializer

```python
class Task(models.Model):
    created_by = models.ForeignKey(Profile)
    created_at = models.DateTimeField(auto_now_add=True)
    title = models.CharField(max_length=150)
    text = models.TextField()
    deadline = models.DateTimeField()


class TaskSerializer(serialzers.ModelSerializer):
    class Meta:
        model = models.Task
        read_only_fields = (
            'created_by', 'created_at', 'update_at')
        fields = '__all__'
```

```python
    fields = 'created_at, title, text'
```
```python
    exclude = 'created_by, update_at, deadline'
```
---

## Validation

---
#### Osztályszintű

```python
class TaskSerializer(serialzers.ModelSerializer):
    ...

    def validate(self, data):
        if timezone.now() >= data['deadline']:
            raise serializers.ValidationError(
                    'Please, enter appropriate deadline.')
        return data 
```

---

#### Mezőszintű

```python
class TaskSerializer(serialzers.ModelSerializer):
    ...
   
    def validate_deadLine(self, value):
        if timezone.now() >= value:
            raise serializers.ValidationError(
                       'Please, enter appropriate deadline.')
        return data 
```
---

#### Validátor
```Python
def deadlineValidator(value):
    if timezone.now() >= value:
        raise serializers.ValidationError(
                'Please, enter appropriate deadline.')

class TaskSerializer(serialzers.ModelSerializer):
    deadLine = serializers.DateTimeField(validators[deadlineValidator])
```

---

### ViewSet
@size[24px](
```python
class UserViewSet(viewsets.ViewSet):
    def list(self, request):
        pass

    def create(self, request):
        pass

    def retrieve(self, request, pk=None):
        pass

    def update(self, request, pk=None):

    def partial_update(self, request, pk=None):

    def destroy(self, request, pk=None):
```
---
### Mixin

- ListModelMixin
- RetrieveModelMixin
- UpdateModelMixin
- DestroyModelMixin
---

### ModelViewSet
- serializer_class
- quryset
- permission_classes

```python
class TasksViewSet(viewsets.ModelViewSet):
    serializer_class= serializers.TaskSerializer
    queryset = models.Task.objects.all()
    permission_classes = (permissions.IsStaffOrReadOnly)
```
---
### Permissons
- Django permissions
    - IsAuthenticated
    - IsAdminUser
    - IsAuthenticatedOrReadOnly
    - ...
--- 
### Custom permissions
```python
class IsStaffOrReadOnly(BasePermission):
    """
    The request is authenticated as a staff,
    or is a read-only request.
    """

    def has_permission(self, request, view):
        return request.method in SAFE_METHODS or 
                request.user and request.user.is_staff
```
---

### Urls
```python
router = routers.DefaultRouter()
router.register(
        r'tasks', views.TasksViewSet, base_name='tasks')
urlpatterns = router.urls
```

---

### Links
- https://github.com/encode/django-rest-framework
- http://www.django-rest-framework.org/tutorial/quickstart/
