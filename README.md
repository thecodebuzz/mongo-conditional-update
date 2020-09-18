# mongo-conditional-update

# https://www.thecodebuzz.com/mongodb-add-new-field-every-document-in-mongodb-collection/

            var collection = db.GetCollection<BsonDocument>("Book");
            var documents = collection.Find(new BsonDocument()).ToList();
            foreach (var rootObject in documents)
            {
                DateTime timestamp = (DateTime)rootObject.GetElement("DateAdded").Value;
                var update = Builders<BsonDocument>.Update.Set("DateAdded", timestamp.AddSeconds(2));
                var filter = Builders<BsonDocument>.Filter.Eq("_id",(ObjectId)rootObject.GetElement("_id").Value);
                var options = new UpdateOptions { IsUpsert = true };
                collection.UpdateOne(filter, update, options);
            }
