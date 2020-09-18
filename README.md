# mongo-conditional-update

# https://www.thecodebuzz.com/mongodb-add-new-field-every-document-in-mongodb-collection/

          CaseI :
          
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
            
            
            case II:
            var bulkOps = new List<WriteModel<BsonDocument>>();
            int i = 0;
            foreach (var record in documents)
            {
                i++;
                var update = Builders<BsonDocument>.Update.Set("DateAdded", ((DateTime)(record.GetElement("DateAdded").Value)).AddSeconds(2));
                var filter = Builders<BsonDocument>.Filter.Eq("_id", (ObjectId)record.GetElement("_id").Value);
                var upsertOne = new UpdateOneModel<BsonDocument>(filter, update) { IsUpsert = false };
                if (i <= 5000)
                {
                    bulkOps.Add(upsertOne);

                    if(i==5000)
                    {
                        collection.BulkWrite(bulkOps);
                        i = 0;
                        bulkOps.Clear();
                    }
                }              
            }
            
