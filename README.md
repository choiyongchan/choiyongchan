from flask import Flask, render_template, request, jsonify
app = Flask(__name__)

from pymongo import MongoClient
client = MongoClient('mongodb+srv://yongchan:qwerty12@cluster0.hr7ebks.mongodb.net/?retryWrites=true&w=majority')
db = client.dbsparta


@app.route('/')
def home():
    return render_template('index.html')

@app.route("/bucket", methods=["POST"])
def bucket_post():
    bucket_receive = request.form['bucket_give']

    bucket_list = list(db.bucket.find({},{'_id':False}))
    count = len(bucket_list) +1

    doc = {
        'num':count,
        'bucket': bucket_receive,
        'done': 0
    }

    db.bucket.insert_one(doc)
    return jsonify({'msg': '등록 완료!'})

@app.route("/bucket/done", methods=["POST"])
def bucket_done():
    num_receive = request.form['num_give']
    db.bucket.update_one({'num': int(num_receive)},{'$set':{'done':1}})
    return jsonify({'msg': '버킷 완료!'})

@app.route("/bucket/re", methods=["POST"])
def bucket_re():
    num_receive = request.form['num_give']
    db.bucket.update_one({'num': int(num_receive)},{'$set':{'done':0}})
    return jsonify({'msg': '버킷 취소 완료!'})

@app.route("/bucket", methods=["GET"])
def bucket_get():
    bucket_list = list(db.bucket.find({}, {'_id': False}))
    return jsonify({'buckets': bucket_list})

if __name__ == '__main__':
    app.run('0.0.0.0', port=5000, debug=True)
