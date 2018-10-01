## Django REST Framework

---

#### Requirements

- Python (2.7 - 3.6)
- Django==2.0.8 (1.10, 1.11, 2.0)

#### Instalation

- djangorestframework

---

### Settings

```python
INSTALLED_APPS = (
     ... 
     'rest_framework', 
     )
```

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

### Validation
Osztályszintű

```python
class TaskSerializer(serialzers.ModelSerializer):
    ...

    def validate(self, data):
        if timezone.now() >= data['deadline']:
            raise serializers.ValidationError(
                    'Please, enter appropriate deadline.')
        return data 
```
Mezőszintű
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

Validátor
```Python
def deadlineValidator(value):
    if timezone.now() >= value:
        raise serializers.ValidationError(
                'Please, enter appropriate deadline.')

class TaskSerializer(serialzers.ModelSerializer):
    deadLine = DateTimeField(validators[deadlineValidator])
```

---

### ViewSet
- serializer_class
- permission_classes
- quryset
    - get_qeryset 
- mixim

---

### Permissons  

--- 

### Urls